# youtube-skills

Claude Code plugin marketplace with shared skills for a multi-channel YouTube operation built on the
yt-pipeline web-app.

| Skill | For | Covers |
|---|---|---|
| `producing-youtube-videos` | channel sessions | asking the owner for GPU runs and recording them, Kaggle TTS runs, local GPU queue and rendering, final render, R2 uploads and cleanups |
| `starting-a-new-channel` | a fresh session for a new channel | research, owner decisions, yt-pipeline setup, resource request to the owner, project folder, first idea |
| `orchestrating-gpu-and-r2` | retired 27 Nov 2026 | historical record only; there is no Orchestrator session |

There is no Orchestrator session since 27 Nov 2026 (owner decision). The shared folder in the `youtube-file` repo is
`manager/` (formerly `_bersama-youtube/`, then `orchestrator/`).

The yt-pipeline template stays the source of truth for **what** each stage delivers (documents, checklist,
required tools, gates). These skills cover **how** to do the shared technical parts. Channel-specific craft
(visual kits, map tooling, character design) stays in each channel's own repository.

## Install

```bash
claude plugin marketplace add <github-owner>/youtube-skills
claude plugin install youtube-production@youtube-skills
```

Or inside Claude Code: `/plugin marketplace add <github-owner>/youtube-skills`, then install `youtube-production`.
Restart running sessions so the skills load.

## Local tools the skills expect

- Kaggle CLI 2.2.5+ with a token in `~/.kaggle/`
- yt-pipeline MCP server (`ytpipeline`), authenticated
- `gpu-lock` and `gpu-monitor` on `PATH` (shared folder `manager/tools/`)
- ffmpeg with NVENC; Remotion per channel project

No credentials, tokens or channel data belong in this repository.

## Changing a skill

Follow `TESTING.md`: describe the failure first, change the skill, then re-run the scenario.
