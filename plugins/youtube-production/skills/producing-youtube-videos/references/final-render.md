# Final render, thumbnails, subtitles, Shorts

The template's Final stage lists the deliverables and gate (Izin unggah). This file covers the shared technique.

## Render and loudness

- Render only from segments the owner approved in the Animation stage.
- Loudness target: **−14 LUFS integrated, −1.5 dBTP true peak** (both Short cuts −14 LUFS as well). Measure after the final
  encode, not before.
- Remotion uses its own bundled ffmpeg (CPU libx264). Hardware encode is a separate ffmpeg step afterwards.
- System `ffmpeg`/`ffprobe` on the laptop is 8.1 (`~/.local/bin`); 6.1 stays at `/usr/bin` as fallback. Call plain
  `ffmpeg`, not a hard-coded `/usr/bin/ffmpeg`.

## NVENC for finals

Previews may go straight to NVENC. For a **final**, compare once per channel before switching:

```bash
gpu-lock run "LS LS-0002 nvenc-compare" -- ffmpeg -i master.mov -c:v h264_nvenc -preset p5 -cq 20 -c:a copy nvenc.mp4
ffmpeg -i master.mov -c:v libx264 -crf 18 -preset slow -c:a copy x264.mp4
```

Compare size and visible quality (text edges, gradients, fast motion). If NVENC is visibly worse, the final stays
libx264 and the report says why.

## QC before asking for Izin unggah

- Watch-video QC: transcript matches the script (spelling-only differences are fine), frames at start/middle/end,
  no black frames, no cut tails.
- Subtitle `.srt` generated from the script and timeline, not from Whisper.
- Thumbnails: the number the tier requires (usually 3: base, `-b`, `-c`), JPEG 1280×720, < 2 MB, for
  "Test & compare". Three different concepts with a competitor contact sheet attached to Izin unggah — see
  `hooks-thumbnails-schedule.md`.

## Shorts: one production, two cuts

Every Short is produced once and rendered as **two cuts** (owner decision, 24 Nov 2026):

| Cut | Length | Goes to | File name |
|---|---|---|---|
| short | 30–45 s | YouTube Shorts | `<CODE>_short-NN.mp4` |
| long | 60–75 s (Zeva Animasi up to 90 s) | TikTok (Creator Rewards counts only videos over 1 min), Instagram/Facebook Reels | `<CODE>_short-NN_panjang.mp4` |

- **Script:** write the long cut; mark the lines dropped for the short cut (e.g. `[POTONG]`). The short cut must make
  sense on its own, hook included.
- **Audio:** TTS runs once, for the long cut. The short cut is edited from the same clips — no extra GPU run, no pitch
  change.
- **Render:** both cuts vertical 1080×1920, −14 LUFS, same safe area (top 210 px, bottom 380 px, right 140 px).
  Check both durations are inside their range before asking for Izin unggah.
- **Publish:** YouTube gets the short cut; TikTok and Meta posting packages use the long cut.
- **Upload:** the short cut is `kind: "short"`, the long cut is `kind: "short_panjang"`. Only `short` goes to YouTube;
  posting packages for TikTok/Meta take `short_panjang`, and cleanup versions each cut separately. If your
  `create_upload` schema does not list `short_panjang`, the session holds an old copy of the tool list: reconnect
  `ytpipeline` (or restart the session) and reload the schema. Only if it is still missing, upload as `kind: "lain"`
  with the `_panjang` file name (the web-app still routes it to TikTok/Meta) and say so in `R2-SELESAI`. A revision of either cut uses `replace_asset_id`
  of that same cut.
- Exact lengths may follow the content as long as they stay in range. Shorts already past Audio when a template
  changes are not redone unless the owner asks.

Other Short rules:
- Count per tier comes from `get_channel` → `tierRules` (it differs per channel).
- How Shorts are tracked depends on the channel's template version: newer templates make each derived Short its
  own Short video (`create_video` format short; derived from a long video with `parent_code` → `LS-0003-S01`,
  standalone → `LS-S0001`); older ones use `short-NN` variants on the long video. Follow what `get_video` / `get_channel` show — never assume from this file.
- File names always contain `short-NN`; for a Short with its own code, NN is its S number (`AC-0004-S03_short-03.mp4`,
  `LS-S0007_short-07.mp4`). Missing Shorts or thumbnails make the Izin unggah request fail.

## Upload

Follow `r2-assets.md`. Large finals need `R2-MINTA` first; a re-render of an existing final uses `replace_asset_id`.
