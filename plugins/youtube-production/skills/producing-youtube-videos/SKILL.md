---
name: producing-youtube-videos
description: Use when a channel session works on a yt-pipeline video (long or Short) at any stage, especially Audio/TTS on Kaggle, Animation or Final render, uploading files to R2, or using the laptop GPU; also when a kernel is QUEUED, a gate is rejected, or the Orchestrator is unreachable.
---

# Producing YouTube Videos (channel sessions)

## Overview

The yt-pipeline **template** says WHAT each stage delivers (documents, checklist, required tools, gate).
This skill says HOW to do the shared technical parts so four channels stop repeating the same mistakes.
Never copy template content into notes or code; read it fresh each time.

## Start of every stage

1. `get_video <code>` (and `get_channel` for brand/tier) → read the current stage's `instructions_md`, `checklist`, `tools`.
2. Use only the listed tools; required ones are mandatory. Report them honestly in `tools_used` as `server.tool` or the built-in name (`Bash`, `SendMessage`).
3. Refer to gates by **name** ("Izin unggah", "Suara disetujui"), not number — numbering changed (Izin unggah is G7 in current templates).

## Hard rules (no exceptions, even under deadline)

| Rule | Why it exists |
|---|---|
| No `kaggle kernels push` before the Orchestrator replies `GPU-IZIN` | Kaggle allows 2 batch GPU sessions per account (QUEUED counts); 30 h/week shared by four channels |
| Never call `log_gpu_session` / `update_gpu_session` yourself | Only the Orchestrator records GPU sessions; two writers double-count |
| A QUEUED kernel is never re-pushed or cancelled by you — report it | Re-pushing created hidden duplicate versions that later ran and burned quota |
| Every job on the NVIDIA GPU runs inside `gpu-lock run "<CH> <code> <job>" -- <cmd>` | Two jobs at once collided and corrupted benchmarks; VRAM is only 4 GB |
| A new version of an existing file uploads with `replace_asset_id` | Uploading as a new asset stacks old versions in R2 |
| Short files are named `…short-NN…` (e.g. `AC-0002_short-01.mp4`) | The web-app matches `short-NN`; `short01` locks the "Upload to YouTube" button |
| Send `R2-MINTA` before any file > 100 MB or batch > 300 MB | R2 budget is shared; quota per channel |
| Every Short has two cuts: 30–45 s for YouTube, 60–75 s (Zeva ≤ 90 s) for TikTok/Reels, one TTS run | TikTok pays only for videos over 1 min; YouTube Shorts perform best short |
| No pitch shift before the owner has listened to the original clip | Shifted clips sounded robotic to the owner; six were reverted to the Kaggle originals |
| Uji / audisi runs need the owner's words in your chat; quote them in `GPU-MINTA` | Tests are the owner's decision, not the channel's |

## Which reference to read

| You are about to… | Read |
|---|---|
| send any message to the Orchestrator | `references/orchestrator-reporting.md` |
| run TTS (or any notebook) on Kaggle | `references/kaggle-tts.md` |
| render, encode, transcribe or generate images on the laptop | `references/local-gpu.md` |
| make the final render, thumbnails, subtitles, Shorts | `references/final-render.md` |
| upload, replace or clean up files in R2 | `references/r2-assets.md` |

## Red flags — stop and re-read the hard rules

- "The kernel has been queued for an hour, I'll push again." → report, don't push.
- "Orchestrator hasn't answered, the deadline is close, I'll push anyway." → ask the owner in your own chat; only the owner's explicit words override.
- "A slot looks free, I'll push now without telling anyone." → only with a `GPU-IZIN` that covers it; send `GPU-MULAI`.
- "The pitch drifted a little, I'll fix it with rubberband first." → deliver the original, name the clips, let the owner decide.
- "This Short is only 35 s of content, one cut is enough." → write the 60–75 s long cut; the short cut comes from it.
- "It's a small render, no need for gpu-lock." → every NVIDIA job uses the lock.
- "I'll upload v2 as a new asset to keep v1 for comparison." → replace; say in `R2-SELESAI` if the owner wants v1 kept.
- "The template says log_gpu_session is required." → outdated template text; the Orchestrator logs. Report it.

## Common mistakes

| Mistake | Fix |
|---|---|
| Checking `kaggle kernels status owner/slug` only | Status is for the newest version; check `owner/slug/<version>` |
| Estimating GPU minutes from utterance count | Estimate from target audio seconds (see `kaggle-tts.md`) |
| Treating `angle-egl` as "CPU render" | It renders on the Intel iGPU; NVIDIA is `--gl=vulkan` |
| Leaving a `chrome-headless-shell` process after a render | Confirm `nvidia-smi` is clean before the lock is released |
| Counting Whisper spelling differences as failed clips | Only real mispronunciations, cut tails or pitch drift are failures |
