# Local GPU (production laptop)

Hardware: NVIDIA GeForce RTX 2050, 4 GB VRAM, plus an Intel UHD iGPU (Optimus laptop).
Policy: **GPU first, CPU only as fallback**. Using the CPU is allowed only when the GPU is busy, VRAM is
insufficient, the GPU result fails quality, or the feature has no GPU path (e.g. ffmpeg audio filters) — say which
in your report.

## The queue: gpu-lock

Every job on the NVIDIA GPU (vulkan render, NVENC encode, Whisper CUDA, image generation) runs through the lock:

```bash
gpu-lock run "SI SI-0005 final-render" -- npx remotion render ... --gl=vulkan
gpu-lock run -w 900 "KT KT-0001 whisper-qc" -- python qc.py   # give up after 15 min (exit 75)
gpu-lock status                                               # who holds the GPU
```

A second job waits automatically. The lock only works if every session uses it. Run the command in the
foreground inside the lock, and check `nvidia-smi` shows no leftover `chrome-headless-shell` before it is released.

Monitor live load with `gpu-monitor` (add `--log run.csv` to record a benchmark). Do not use the Ubuntu `nvtop`
package — it crashes on the Intel iGPU.

## Choosing the Remotion renderer (per video)

| Renderer | Runs on | gpu-lock? | Best for |
|---|---|---|---|
| `--gl=angle-egl` (default) | Intel iGPU — this is **not** CPU rendering | no | SVG, light three.js; leaves NVIDIA free |
| `--gl=vulkan` | RTX 2050 | **yes** | heavy canvas/map scenes measured faster on NVIDIA |

Measured (1080p, concurrency 3): globe three.js 450 frames Intel 47 s vs vulkan 64 s; satellite map 180 frames
Intel 45 s vs vulkan 37 s; SVG characters 300 frames ≈ 12 s on every path. Measure once per scene type with a short
render, then record the choice in the channel's own instructions. `--gl=vulkan` selects the RTX 2050 with or without
PRIME environment variables; PRIME variables only change `angle-egl` (and make it slower). `--gl=angle` and
`--gl=egl` disable WebGL.

Lower `--concurrency` if VRAM goes above ~3.5 GB.

## Other GPU work on the laptop

- **Whisper QC**: faster-whisper with `device="cuda"`; medium, or large-v3 with `compute_type="int8_float16"`.
  The shared venv `~/.venvs/watch-video` already loads the CUDA libraries (no `LD_LIBRARY_PATH` needed).
- **Encode**: `h264_nvenc` / `hevc_nvenc`, decode with `-hwaccel cuda`. AV1 encode is not supported by this GPU.
- **Images**: SD 1.5 (+ LCM/Turbo) is the main path; SDXL only with `--lowvram`; upscale and background removal
  are fine. Store models once for all channels (e.g. `~/models/`).

## Not on the laptop

TTS (VoxCPM2), generative video (Wan, LTX-Video, HunyuanVideo) and Flux need ≥ 8 GB → Kaggle, through `GPU-MINTA`.
