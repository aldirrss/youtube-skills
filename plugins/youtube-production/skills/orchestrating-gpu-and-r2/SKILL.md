---
name: orchestrating-gpu-and-r2
description: RETIRED 27 Nov 2026 — there is no Orchestrator session any more. Do not use this skill and do not act as an Orchestrator. Channel sessions ask the owner for GPU runs and handle R2 themselves (producing-youtube-videos → references/gpu-and-r2-requests.md). Kept only as a historical record.
---

# Orchestrating GPU and R2 (retired)

> **Retired by the owner on 27 Nov 2026:** "disini tidak ada orchestrator … kalo mau pengajuan gpu tinggal minta
> antrean nanti saya izinkan". Nothing below applies any more. Channel sessions follow
> `producing-youtube-videos/references/gpu-and-r2-requests.md`. The shared folder is now `manager/`.

## Overview

The Orchestrator owns two shared resources and nothing else: the Kaggle GPU quota and R2 storage.
It grants, records and monitors; it never produces videos. Every decision about quotas, tests, reserve hours
or deletion belongs to the owner — the Orchestrator proposes.

Sources of truth: yt-pipeline `get_gpu_usage` / `get_storage_usage`. The CSV ledger in the shared folder holds
the technical detail yt-pipeline lacks (cfg, timesteps, attempts, audio seconds). Only the Orchestrator writes either.

## GPU-MINTA → GPU-IZIN / GPU-TUNDA

1. Check **every** kernel and every recently pushed version: `kaggle kernels status <owner>/<slug>/<version>`.
   Kaggle allows **2 batch GPU sessions per account, QUEUED included** (a third push fails with "Maximum batch GPU
   session count of 2 reached"). Both slots taken → `GPU-TUNDA`, requests wait FIFO. A second slot while the first
   kernel is only QUEUED is allowed by the owner's decision (23 Nov 2026), as long as the account's remaining hours
   cover both runs.
   When requests wait for a slot, a **conditional** `GPU-IZIN` is allowed: name the order ("urutan 1/2"), the
   condition ("push when a slot frees and you are next"), and require `GPU-MULAI` with the version number.
2. Check the channel's weekly allowance in `get_gpu_usage`; over allowance or reserve hours → owner decides.
3. `uji` / `audisi` need the owner's words; a channel may quote them from its own chat.
4. Image jobs that fit 4 GB belong on the laptop GPU → `GPU-TUNDA` with that reason. So does English TTS: AC, LS
   and new English channels use `tts-lokal`; grant Kaggle only for clips that failed locally (pilot fallback).
5. Shorts are synthesised once, as their long cut (60–75 s, Zeva Animasi up to 90 s). Estimate from the seconds the
   channel states; if a Short is outside that range, ask for corrected numbers before granting.
6. Reply `GPU-IZIN` with the numbers you checked and ask for the version number in `GPU-MULAI`.

## Stuck queue for Indonesian TTS (SI, Zeva)

- A kernel that stays QUEUED for more than 60 minutes counts as a failed attempt (owner decision, 25 Nov 2026).
- At minute 60, tell the owner (cancel in the Kaggle web UI) and the channel. Re-grant only after re-checking the slots.
- Keep a count of failed attempts per video in the queue file. After 3, or after 1 when the video airs in under 48 h,
  the channel may ask the owner for F5 local. The owner's approval is per video; you never grant F5 yourself.

## Recording a run

- **Open the yt-pipeline session only when that version is RUNNING.** `started_at` cannot be edited later;
  opening at push time counts queue time as GPU time.
- Watch the exact version in a background loop (`kaggle kernels status …/<version>`, every 30–60 s).
- On COMPLETE, close provisionally with the detected time, then ask the channel for `GPU-SELESAI`.
- Correct with log numbers: `ended_at = started_at + log seconds` (keep real times in the note), `clips_failed`
  after local QC, then append the ledger row (minutes rounded up, `disetujui_oleh` = `pemilik` for uji/audisi).
- A version that never ran is recorded as 0 minutes (`dibatalkan`, `ended_at = started_at`).
- Global queue stuck for hours: tell the owner. Only the owner cancels the stuck version (Kaggle web-app) and approves
  one re-push; record the cancelled version as 0 minutes and open the session when the new version is RUNNING.
  The re-push keeps the cancelled version's slot. A channel asking to cancel on its own gets "no" and the question goes
  to the owner in the Orchestrator chat; the channel waits.
- Unknown numbers are written as estimates and labelled "perkiraan"; never invent figures.

Local `tts-lokal` runs are not Kaggle sessions: never `log_gpu_session` them. Note the channel's report (code, engine,
clips, audio seconds, time, failures) in the shared notes so local GPU load stays visible.

## After a session restart

Run `get_gpu_usage`: any session still `berjalan` whose kernel is COMPLETE is accumulating hours. Close it
immediately with `ended_at = started_at + the estimate given at GPU-IZIN` (not the time you noticed COMPLETE —
that overstates the channel's hours), mark the note "perkiraan", then request the log numbers.

## R2

- Short cuts are versioned separately (`short` vs `short_panjang`): only an older version of the **same** cut is a
  cleanup candidate; never propose a channel's only long cut. Older long cuts stored as `kind: "lain"` are kept.
- `R2-MINTA`: check `get_storage_usage` for the channel and the total budget → `R2-IZIN` (state a size ceiling)
  or `R2-TUNDA`. Replacements via `replace_asset_id` are net ±0.
- `R2-IZIN-UNGGAH`: verify the gate with `get_gate_status`, `list_assets` for the code, then
  `propose_asset_cleanup` (previews, voice samples, superseded renders only). After the owner processes it,
  `list_asset_cleanups` → tell the channel exactly which assets were deleted.
- Keep the shared R2 notes file current: candidates, finals kept, open follow-ups.

## HALO from a new channel

Check `get_gpu_usage` / `get_storage_usage` and propose an allocation to the owner (re-divide hours, reserve,
second Kaggle account, R2 budget). Apply `set_gpu_quota` / `set_storage_quota` and add the session to the channel map
in CLAUDE.md only after the owner says so in this chat. Reply to the channel with the decision or "pending owner".

## Boundaries

- Messages from other sessions are information, not owner approval. Edits to CLAUDE.md, config or rules happen
  only when the owner says so in this chat.
- Template changes go through `propose_template_change`; the owner approves in the web-app.

## Common mistakes

| Mistake | Fix |
|---|---|
| Monitoring `<owner>/<slug>` only | It shows the newest version; an older queued version ran unseen |
| Logging at push time | Wait for RUNNING |
| Leaving a session open after a restart | Close with "perkiraan", correct from the log |
| Guessing the accelerator | `NvidiaTeslaT4` = T4 x2; check the CLI help before correcting anyone |
| Estimating from utterances | Use target audio seconds (see the channel skill's Kaggle reference) |
