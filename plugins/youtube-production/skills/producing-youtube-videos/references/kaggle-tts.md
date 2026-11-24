# Kaggle TTS runs

TTS (VoxCPM2 voice cloning, ~8 GB VRAM) runs on Kaggle because the laptop GPU has 4 GB.
**English channels (AC, LS, and any new English channel) use `tts-lokal` on the laptop instead** — see
`local-gpu.md`. Kaggle stays their fallback for single clips that fail locally. Indonesian channels (SI, Zeva) stay on Kaggle.
One Kaggle account (`utama` in yt-pipeline), 30 GPU hours per week, reset Saturday 00:00 UTC, shared by all channels.

## Indonesian channels (SI, Zeva): Kaggle first, F5 local as owner-approved fallback

Owner decision, 25 Nov 2026 (`ATURAN-KAGGLE-GPU.md` §4.9, v1.15).

- **Kaggle first.** VoxCPM2 on Kaggle is the main engine, through the usual GPU-MINTA flow.
- **What counts as a failed attempt:** the kernel ERRORs, or stays QUEUED for more than 60 minutes. Clips that fail QC
  are not a failed attempt; re-run them with `--hanya`.
- **You cannot cancel a queued kernel** (the CLI has no cancel command). At minute 60 the Orchestrator tells the owner,
  and the owner cancels in the Kaggle web UI. Re-push only after a new GPU-IZIN.
- **After 3 failed attempts** for the same video, ask the owner in your chat to use F5-TTS-INDO-FINETUNE-V2 locally for
  that video, with a listening sample. Use F5 only after the owner agrees, and quote their words in the report.
- **Shortcut:** if the video airs in under 48 hours, you may ask after 1 failed attempt.
- **Running F5:**
  - path `/mnt/data/Project/ai/F5-TTS-INDO-FINETUNE-V2`, inside `gpu-lock`, with `--model F5TTS_v1_Base`;
  - 1–3 sentences per clip; numbers and acronyms written phonetically;
  - same QC as Kaggle, no pitch shift;
  - report with `TTS-LOKAL-SELESAI`;
  - its licence is not listed in the credits (owner decision).

## Before pushing

1. Text normalised for TTS (numbers to words, pronunciation dictionary) — channel tooling.
2. Dataset version uploaded (`kaggle datasets version -p <input-folder>`).
3. `GPU-MINTA` sent and `GPU-IZIN` received (see `orchestrator-reporting.md`).
4. Push with the channel's push script if it has one (it refuses to push while another kernel is active), otherwise
   `kaggle kernels push -p <kernel-folder> --accelerator NvidiaTeslaT4`.

Kaggle allows **at most 2 batch GPU sessions per account, and QUEUED versions count**. A third push is rejected with
"Maximum batch GPU session count of 2 reached". The Orchestrator assigns the two slots; a conditional `GPU-IZIN`
("push when a slot frees") means: check both slots per version, push only when one is free and you are next in line,
then send `GPU-MULAI`. A `GPU-IZIN` covers the request it answered (codes, audio seconds). Changed scripts or a
different batch need a new `GPU-MINTA`.

`NvidiaTeslaT4` means **T4 x2** (Kaggle CLI 2.2.5 help text). The notebook can use GPU 1 for TTS and GPU 2 for Whisper QC.

## Watching a run

- Status of one specific version: `kaggle kernels status <owner>/<slug>/<version>`.
  Plain `<owner>/<slug>` reports only the **newest** version — an older queued version can start running unseen.
- QUEUED for a long time is normal (observed 0 min to almost 4 h). Quota is not consumed while queued.
  **Never push again to "unstick" it**: the old version stays in the queue and runs later, doubling the cost.
  Report the wait to the Orchestrator. If the global queue is stuck for hours, the **owner** may cancel the stuck
  version in the Kaggle web-app and approve one re-push; the cancelled version is recorded as 0 minutes.
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

- One run per video: long + all Shorts in one config. Shorts are synthesised once, as their long cut (60–75 s,
  Zeva up to 90 s); the short cut is edited from those clips. Estimate with the long-cut seconds.
- Re-runs only for clips that truly failed, via the config's `"hanya": [...]` list.
- Max 3 attempts per utterance (2 for channels without a pitch gate).
- On Kaggle keep QC cheap (last word present, tail not cut, pitch gate). Full Whisper QC runs locally on the laptop GPU.
- Whisper spelling differences are **not** failures.
- Never pitch-shift (rubberband) a clip before the owner has listened to the original. Deliver it as generated, say
  which clips drift; shift only the clips the owner names. A suspected drift is not a reason for another GPU run either.
- Owner-approved acting (shouted or surprised lines with higher pitch) is not a failure either.

## After the run

Report `GPU-SELESAI` with numbers from the kernel log (last cell time), then the counts after local QC.
