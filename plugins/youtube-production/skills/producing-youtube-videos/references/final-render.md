# Final render, thumbnails, subtitles, Shorts

The template's Final stage lists the deliverables and gate (Izin unggah). This file covers the shared technique.

## Render and loudness

- Render only from segments the owner approved in the Animation stage.
- Loudness target: **−14 LUFS integrated, −1.5 dBTP true peak** (Shorts −14 LUFS as well). Measure after the final
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
  "Test & compare".

## Shorts

- Count per tier comes from `get_channel` → `tierRules` (it differs per channel).
- Vertical 1080×1920; keep text inside the safe area (top 210 px, bottom 380 px, right 140 px).
- How Shorts are tracked depends on the channel's template version: newer templates make each derived Short its
  own Short video (`create_video` format short with `parent_code`, codes like `LS-S0001`); older ones use `short-NN`
  variants on the long video. Follow what `get_video` / `get_channel` show — never assume from this file.
- File names always contain `short-NN` (`<CODE>_short-01.mp4`). Missing Shorts or thumbnails make the Izin unggah
  request fail.

## Upload

Follow `r2-assets.md`. Large finals need `R2-MINTA` first; a re-render of an existing final uses `replace_asset_id`.
