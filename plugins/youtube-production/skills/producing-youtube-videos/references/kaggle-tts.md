# Kaggle TTS runs

TTS (VoxCPM2 voice cloning, ~8 GB VRAM) runs on Kaggle because the laptop GPU has 4 GB.
One Kaggle account (`utama` in yt-pipeline), 30 GPU hours per week, reset Saturday 00:00 UTC, shared by all channels.

## Before pushing

1. Text normalised for TTS (numbers to words, pronunciation dictionary) — channel tooling.
2. Dataset version uploaded (`kaggle datasets version -p <input-folder>`).
3. `GPU-MINTA` sent and `GPU-IZIN` received (see `orchestrator-reporting.md`).
4. Push with the channel's push script if it has one (it refuses to push while another kernel is active), otherwise
   `kaggle kernels push -p <kernel-folder> --accelerator NvidiaTeslaT4`.

`NvidiaTeslaT4` means **T4 x2** (Kaggle CLI 2.2.5 help text). The notebook can use GPU 1 for TTS and GPU 2 for Whisper QC.

## Watching a run

- Status of one specific version: `kaggle kernels status <owner>/<slug>/<version>`.
  Plain `<owner>/<slug>` reports only the **newest** version — an older queued version can start running unseen.
- QUEUED for a long time is normal (observed 0 min to almost 4 h). Quota is not consumed while queued.
  **Never push again to "unstick" it**: the old version stays in the queue and runs later, doubling the cost.
  Report the wait to the Orchestrator; only the owner cancels or re-pushes.
- Never open the notebook in the Kaggle web editor during a run — an interactive session also reserves GPU quota.
- Output of a finished version: `kaggle kernels output <owner>/<slug>/<version> -p <folder>`.

## Estimating cost

Estimate from the **target audio length**, not the number of utterances:

```
minutes ≈ 4–5 (install + model load) + rate × target_audio_seconds
rate on T4 x2: ≈ 0.13–0.14 (long narration) · ≈ 0.09–0.12 (short dialogue lines)
```

Measured: 287 s audio → 45 min; 532 s → 66 min; 170 s of dialogue → 24 min; 86 s → 13 min.

## Keeping runs cheap

- One run per video: long + all Shorts in one config.
- Re-runs only for clips that truly failed, via the config's `"hanya": [...]` list.
- Max 3 attempts per utterance (2 for channels without a pitch gate).
- On Kaggle keep QC cheap (last word present, tail not cut, pitch gate). Full Whisper QC runs locally on the laptop GPU.
- Whisper spelling differences are **not** failures. Small pitch drift (≤ ±2.5 semitones) is fixed locally with
  rubberband, not by another GPU run.
- Owner-approved acting (shouted or surprised lines with higher pitch) is not a failure either.

## After the run

Report `GPU-SELESAI` with numbers from the kernel log (last cell time), then the counts after local QC.
