---
name: starting-a-new-channel
description: Use when the owner wants to start a new YouTube channel in the yt-pipeline setup — a fresh session in an empty channel folder, a prompt like "buat channel baru", "set up a new channel from scratch", or a channel slug that does not exist yet in list_channels.
---

# Starting a New Channel

## Overview

One owner prompt can take a channel from idea to "ready for the first video", but **three things only the owner
can do**: decide the direction, create the YouTube channel, and press "Tambah channel" in the yt-pipeline web-app
(there is no `create_channel` MCP tool). GPU hours and R2 storage are granted by the Orchestrator and decided by
the owner — never by the new channel. Everything else this session does itself, in the order below.
"Kerjakan sekaligus" means few stops, not zero stops: bundle each stop into one message.

## The run, with its stops

| Phase | Who | What |
|---|---|---|
| 1. Research | session | `list_channels` (taken prefixes/handles, incl. retired ones); `get_channel` of the sibling whose `pipeline.version` is highest; `get_gpu_usage`, `get_storage_usage`; yt-research on the niche **in every candidate language/market**: `yt_analyze_keyword`, `yt_search_query`, `yt_analyze_competitors`, `yt_trending` |
| **STOP 1** | owner | 2–3 directions, each with: name, handle ideas, audience, content language & market, long/Short mix and duration tier, posting rhythm, visual style & palette/tone, narrator approach (cloned reference voice, owner's voice, …), 3–5 pillars, sensitive-topic limits, a free 2-letter code prefix, research numbers. Plus the resource warning (below). Recommend one; let the owner choose — an explicit choice, not "terserah" |
| **STOP 2** | owner | Owner creates the YouTube channel/handle, presses "Tambah channel" (name, handle, prefix), and makes sure the MCP token covers the new slug. Ask in the same message for existing social handles and the session name to use. Wait for "sudah" |
| 2. Configure yt-pipeline | session | `get_channel(<slug>)` — if not visible, stop: token or web-app step missing. Then `update_brand_kit`, `upsert_pillar` × N, `update_tier_rules`, `update_rotation`, `upsert_social_profile` (only given handles), and `propose_template_change` for **both** `long` and `short` templates, every stage: copy the structure and tools of the newest sibling template, write this channel's own notes. Videos follow approved template versions automatically, so you need not wait for approval to continue |
| 3. Resources | session | `SendMessage` `HALO` to the Orchestrator (format below). Never call `set_gpu_quota`, `set_storage_quota`, `set_storage_budget` |
| 4. Project folder | session | Below |
| 5. Ideas | session | `create_idea` × 3–5 → per idea `get_idea` (read `idea_tools`), competitor data with those tools only, `add_idea_competitor`, `score_idea` with honest `tools_used` |
| **STOP 3** | owner | Ranked ideas → owner picks one; narrator/voice audition needs the owner's words (then `GPU-MINTA audisi`, only after quota is settled and `GPU-IZIN`); remind the owner to approve the template proposals in the web-app |
| 6. First video | session | `create_video` for the picked idea; continue with **producing-youtube-videos** |

## Resource warning (always in STOP 1)

The shared pools may already be fully allocated (as of 23 Nov 2026: 4 × 6 h GPU + 6 h reserve = the account's
30 h/week; 4 × 10 GB R2 = the whole 40 GB budget). Quote the current numbers and the options: re-divide hours,
use the reserve, a second Kaggle account, raise the R2 budget or lower per-channel quotas. If the owner states
numbers here ("samakan 6 jam, 10 GB"), pass them on in `HALO` as the owner's wish and tell the owner the
Orchestrator applies them only after the owner confirms in the Orchestrator's chat, including where the hours come from.

## HALO message

```
HALO
Channel: <name> · slug <slug> · code prefix <XX> · kernel prefix <xx>-* · session "<session name>"
Owner direction: <one line> · content language: <id/en>
Request: weekly GPU allowance <h> and R2 quota <GB> — owner's wish: "<quote>", pending owner decision (pools may be full)
```

The session name comes from the owner (STOP 2) or the header line of `ListAgents` ("This session is …").

## Project folder

The current folder becomes the channel root (rename to the slug only if the owner asks).

```
CLAUDE.md          # channel rules + Orchestrator block (_bersama-youtube/PROTOKOL-ORCHESTRATOR.md §5) + "use producing-youtube-videos"
00_strategi/       # chosen direction, audience, pillars, sensitive-topic limits
20_aset-bersama/   # logo, avatar, music, narrator reference — add_license BEFORE any asset is used
30_produksi/       # one folder per video code
40_tools/          # copied from the sibling closest in style (its INVENTARIS-ALAT.md lists what it uses)
```

- Copy tooling **without** `node_modules`, `.venv`, `out/`, caches, `.env`, `kaggle.json` or any token; run `npm ci`
  in `studio-remotion`.
- Rename to the new prefix: Kaggle kernel `<owner>/<xx>-produksi-suara`, datasets `<xx>-referensi-suara`,
  `<xx>-NNNN-naskah` (create datasets at the Audio stage, not now); watermark/handle in Remotion components.
- Repository language follows the owner's global instruction file; if siblings differ, say so once and ask.

## Red flags

| Thought | Reality |
|---|---|
| "The owner said do everything, so I'll choose the name and pillars." | Direction is STOP 1 — recommend, then wait |
| "I'll create the channel through the browser/API." | Owner presses "Tambah channel"; you never create accounts |
| "The owner said 6 h / 10 GB, so I'll set it." | Owner's numbers go into HALO; the Orchestrator applies after the owner confirms there |
| "I'll run a quick narrator audition on Kaggle." | Audition = owner's words + `GPU-MINTA` after quota is settled |
| "I'll copy the sibling's template text and brand." | Copy the structure; notes and brand describe this channel |
