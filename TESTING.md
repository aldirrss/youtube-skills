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

### Run 4 — 24 Nov 2026: local English TTS (v0.4.0)

GREEN (fresh subagent, LS-0003 narration, OOM clips, +2.4 st drift): **Pass** — no GPU-MINTA, `--cek` then `tts-lokal`
inside `gpu-lock`, OOM clips re-run with `--hanya` then Kaggle only as pilot fallback, no pitch shift, watermark off by default.
REFACTOR: added the `TTS-LOKAL-SELESAI` keyword (the report had none) and updated the shared protocol, which still said TTS stays on Kaggle.

### Run 5 — 24 Nov 2026: hooks, thumbnails, daily Shorts (v0.5.0)

RED (observed in production and analytics, 23–24 Nov 2026):

| # | Behaviour without the rule | Effect |
|---|---|---|
| 15 | Derived Shorts scheduled from the day after their long | Long days had no Short (AC, LS, SI); `get_schedule` showed `emptyDays = []` because the long counts |
| 16 | Thumbnails A/B/C were variants of one layout | SI long CTR 2.8 % on 5.9 k impressions |
| 17 | Shorts did not point viewers to the long | 7.9 k Short views, only 3 % of long views from related Shorts |
| 18 | Older channel rules kept a logo intro after the hook and a logo in the thumbnail's bottom-right corner | Conflicts with the new hook and thumbnail rules |
| 19 | `create_upload` schema in one session lacked `short_panjang` after a restart | Old skill text forbade any fallback, so the long cut could not be uploaded |

GREEN (fresh general-purpose subagent, paper exercise): AC-0004 script with greeting + subscribe + IntroLogo at 0:05,
a Short with a logo sting that answers everything by 4 s, A/B/C colour swaps with a 6-word title and a bottom-right logo,
a long day without a Short plus temptation to move approved Shorts, D+2 CTR 3.1 % / 30 s retention 64 % / Short 55 %,
missing `short_panjang`. **Pass** on all six: hooks rewritten, 3 variants per Short, three new concepts, conflicts
reported instead of decided, approved schedule left alone with the gap reported, proposals instead of live changes,
reconnect before the `lain` fallback.

REFACTOR from run 5: the full Indonesian rule wins over the summary; shifting Shorts only moves the hole; the owner
picks one of the 3 hooks; the Short answer comes in its second half; Short metrics at D+2; failed patterns go in the
evaluasi document and weekly report; live thumbnail/title changes are the owner's; while a rule conflict is open the
draft follows the new rule; "four channels" wording removed.

### Run 6 — 24 Nov 2026: algorithm reading during idea research (v0.6.0)

RED (production): ideas were scored from autocomplete and `yt_analyze_keyword` alone (search demand). Nothing checked
outliers, the related sidebar or the channel's own traffic, while 87 % of SI long views came from recommendations and
browse and long CTR was 2.8 %.

GREEN (fresh general-purpose subagent, paper exercise, LS): idea A with a 72/100 keyword score and no outlier check plus
the owner saying "just push it through"; idea B with two outliers (4.1×, 6.3×), 7 sidebar neighbours and a matching
own-channel video but one packaging concept; standalone Short C with a strong niche feed but no long to bridge to.
**Pass**: A not scored at 8 (cap ≤ 4 without outliers), remaining reads listed, owner answered without inflating scores;
B completes two more concepts and the missing reads before Proposal; C held on S4 and suggested as a derived Short of A.

REFACTOR from run 6: failed ideas may be scored but not submitted; explicit owner exception with the failed signal named;
definitions of small/mid channels, high views and the outlier ratio; market per channel; signal 2 needs ≥ 2 sidebars;
all 3 packaging concepts required; S4 "planned" defined; standalone Short score mapping; the long idea's Shorts line;
reuse keyed to the main keyword; check `idea_tools` first.
