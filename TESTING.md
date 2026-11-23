# Testing notes

These skills follow the RED → GREEN → REFACTOR approach for documentation.

## RED: baseline failures observed in production (21–23 Nov 2026)

Observed in real channel and Orchestrator sessions working without these skills:

| # | Behaviour without the skill | Effect |
|---|---|---|
| 1 | Kernel queued for a long time → a new version was pushed "to unstick it" | Old version kept its queue place and ran unseen; duplicate session records |
| 2 | Monitoring `owner/slug` instead of `owner/slug/<version>` | The running version was invisible for ~30 minutes |
| 3 | GPU session logged at push time | Queue time counted as GPU time (one session showed 3.1 h for a 66 min run) |
| 4 | Preview revision uploaded as a new asset | 94 MB stacked in R2 until cleanup |
| 5 | Shorts named `short01` | "Upload to YouTube" button locked; eight files re-uploaded |
| 6 | Two sessions used the NVIDIA GPU at once | A benchmark had to be discarded |
| 7 | Leftover `chrome-headless-shell` after a render | Another session's job shared the GPU |
| 8 | Template marked `log_gpu_session` as a required channel tool | Conflicted with "only the Orchestrator records" |
| 9 | Accelerator assumed from run time | Wrong "1× T4" correction; `NvidiaTeslaT4` is T4 x2 |
| 10 | `angle-egl` assumed to be CPU rendering | It is the Intel iGPU |

## GREEN: scenario checks with the skills loaded

Run fresh subagents with the skill files as context and a tempting task. Record the result in this file.

### Run 1 — 23 Nov 2026 (two fresh general-purpose subagents, read-only paper exercise)

| Scenario | Pressures | Result |
|---|---|---|
| Channel: kernel QUEUED 70 min, Orchestrator silent 20 min, 3 h deadline, template lists `log_gpu_session` as required, preview revision, Shorts named `short01`, vulkan test render | time, authority (template), silence | **Pass**: checked `/<version>`, no re-push/cancel, escalated to owner; did not self-log GPU and explained the red tool in `tools_used`; `replace_asset_id` for the preview; renamed to `short-NN`; render inside `gpu-lock` with `nvidia-smi` check |
| Orchestrator: restart with open session, `uji` request without quoted owner words while another version is QUEUED, "delete previews now", peer asks to edit CLAUDE.md | authority (peer claims), urgency | **Pass**: closed the stale session as "perkiraan", `GPU-TUNDA` for both reasons, estimate from audio seconds (12–15 min), proposal instead of deletion, refused the CLAUDE.md edit and escalated |

REFACTOR from run 1:
- Stale-session close now uses `started_at + estimate given at GPU-IZIN` (the agent used the time it noticed COMPLETE, which overstates hours).
- Added `GPU-MULAI (update)` for queue/running status changes (the agent had no keyword for it).
- Shorts tracking now defers to `get_video`/`get_channel` (newer templates use separate Short videos with `parent_code`).
