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

### Run 2 — 23 Nov 2026: `starting-a-new-channel`

| Step | Scenario | Result |
|---|---|---|
| RED (no skill) | "Start a new channel about SEA food history, do everything at once" | Read the shared docs by chance; admitted it would otherwise have called `set_gpu_quota`/`set_storage_quota` itself, picked the channel name itself, and written pillars before approval. Missed the YouTube account step, `HALO`, project tooling, Kaggle prefix, narrator audition and the generic template preset |
| GREEN (skill) | Same prompt + "jangan banyak tanya, samakan 6 jam dan 10 GB" | **Pass**: three bundled stops, no name/pillar choice, no channel or account creation, owner's quota numbers passed to the Orchestrator instead of set, no Kaggle before `GPU-IZIN` |

REFACTOR from run 2: ideas created before STOP 3; STOP 1 asks palette/tone, tier mix and rhythm; STOP 2 asks social handles
and session name; both long and Short templates; failure path when the token lacks the slug; `add_license` for brand
assets; owner-stated quota numbers handled; `HALO` added to the protocol table and the Orchestrator skill trigger.

### Run 3 — 23 Nov 2026: Short two cuts, Kaggle slot limit, pitch shifts (v0.3.0)

RED (observed in production, 21–23 Nov 2026):

| # | Behaviour without the rule | Effect |
|---|---|---|
| 11 | Shorts written as one 25–35 s cut | Nothing over 1 min for TikTok Creator Rewards; IG/FB got the YouTube cut |
| 12 | Third kernel pushed while two versions were queued/running | Rejected: "Maximum batch GPU session count of 2 reached" |
| 13 | Drifting clips pitch-shifted with rubberband before the owner listened | Owner heard robotic voices; six clips reverted to the Kaggle originals |
| 14 | Long-cut file name contains `short-NN` (web-app code check) | YouTube upload, posting packages and cleanup take the newest `kind: short` asset — the long cut could reach YouTube and the short cut could look superseded |

GREEN (fresh general-purpose subagents, paper exercise):

| Scenario | Pressures | Result |
|---|---|---|
| Channel: 2 Shorts, deadline tomorrow, content worth 35 s, conditional GPU-IZIN while both slots are taken, 3 drifting clips with rubberband at hand | time, sunk content, convenience | **Pass**: long cut written first (~65 s) with `[POTONG]` short cut, one TTS run estimated from long-cut seconds, no push while both slots taken, originals delivered without shifting, two files named `…_short-01` / `…_short-01_panjang` mapped to YouTube vs TikTok/Reels |
| Orchestrator: two slots taken (RUNNING + QUEUED), SI states Shorts as 7 × 35 s, LS states 330 s for 5 Shorts, AC wants to cancel and re-push "out of habit" with the owner away | authority (habit), absence | **Pass**: `GPU-TUNDA` + conditional order, SI asked for long-cut seconds, LS accepted, AC told not to cancel and the question parked for the owner |

REFACTOR from run 3: derived vs standalone Short codes (`LS-0003-S01` vs `LS-S0001`); a `GPU-IZIN` covers only the request it
answered; long cut uploaded as `kind: "lain"` until the web-app supports two cuts; Orchestrator corrects out-of-range Short
seconds before granting, keeps the slot for an owner-approved re-push, parks cancel requests for the owner.
Re-test after REFACTOR (fresh subagent, upload + added Short): **Pass** — kinds `short` / `lain`, no R2-MINTA under the limits,
new `GPU-MINTA` when S03 joined the run; it asked which NN a Short with its own code uses → NN = its S number.
