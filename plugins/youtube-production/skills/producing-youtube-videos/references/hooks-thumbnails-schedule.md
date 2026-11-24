# Hooks, thumbnails and the daily Short schedule

Owner decision, 24 Nov 2026. Full rule (Indonesian), with the data behind it:
`/mnt/data/Project/youtube/_bersama-youtube/ATURAN-HOOK-THUMBNAIL-JADWAL.md`.
This file is a summary; if it and the full rule disagree, the full rule wins. Your channel's template may hold stricter
or extra items; read `get_channel` first.

Why: on Sarjana Ilmupedia (28 days to 23 Nov 2026), 96 % of Short views came from the Shorts feed but only 3 % of long
views came from Shorts. Long CTR was 2.8 %, and 87 % of long views came from recommendations and browse — so thumbnail,
title and the first 30 s decide growth.

## Schedule: one Short every day

- One Short per day, no empty days. A long and a Short may go out on the same day.
- Keep at least 7 days of Shorts that have passed Izin unggah. Check with `get_schedule`.
- **`emptyDays` does not flag a long day that has no Short** (the long counts as an upload). Check every long day yourself.
- On a long's release day, its first derived Short goes out **after the long is public** (e.g. long 02:00 → Short 03:00,
  long 17:00 → Short 19:00) and its "Related video" points to that long.
- If the tier's derived-Short count cannot fill every day, fill the gap with standalone Shorts (`<PREFIX>-S0001`, …).
- Moving an already approved schedule is the owner's decision: report the gap, do not move it yourself. Say in the
  report that shifting a run of Shorts one day earlier only moves the hole to the end of the run; a filled long day
  needs one extra Short (standalone or an extra derived one), which has to pass every stage — ask early.

## Short hook (0–3 s), same in both cuts

- Frame 0 is the strongest or strangest image. No logo, intro, title card or dark frame. Narration starts by 0.2 s.
- The first line opens a curiosity gap (surprising claim, question with stakes, contradiction) and does **not** answer it
  in the first 3 s.
- On-screen text at 0–3 s: at most 6 words.
- Write 3 hook variants in the Short script and show them to the owner at the script gate; the owner picks one.
- The open question stays open through 0–3 s; the Short's own answer comes in its second half.
- The claim must be true and paid off. A misleading hook raises swipe-aways and lowers recommendations.

## Bridge from Short to long

- The Short answers most of its own question and leaves **one** question that the long answers.
- The last 2–3 s name that question and point to the full video. No "link in bio" on YouTube.
- "Related video" is mandatory: derived Short → its parent long; standalone Short → the most relevant long.
- Pinned comment with the long's title. TikTok/IG caption: "full version on YouTube".

## Long hook (0–30 s)

- 0–3 s show and say the thumbnail's promise. Thumbnail, title and hook tell one story.
- 3–30 s: the stakes, a short roadmap, and one open question answered near the end.
- Not in the first 30 s: greeting, logo intro, subscribe ask, source list.
- Re-hook every 60–90 s: a new question, a visual change, or a surprising number at each chapter start.

## Thumbnails

- Three genuinely different concepts (A/B/C), not colour swaps. Turn on "Test & compare".
- One focal point. Text at most 4 words, not repeating the title.
- Must read at 168×94 px. Keep the bottom-right corner empty (the duration label covers it).
- Put the three next to the top 3 competitor thumbnails for the topic (`yt_search_query`). Attach that contact sheet,
  full size and phone size, to the Izin unggah request.

## Targets and the fix loop

| When | Metric | Target | Below target |
|---|---|---|---|
| D+2 long | CTR after ≥ 1,000 impressions | ≥ 4 % (goal 6 %) | Propose a new thumbnail and/or title concept to the owner |
| D+2 long | Still watching at 30 s | ≥ 70 % | Record the cause; the next long's hook must fix it |
| D+2 Short | Stayed to watch | ≥ 60 % (goal 70 %) | Log the hook pattern as failed; do not reuse it |
| D+7 long | Views from related Shorts | ≥ 10 % | Fix the closing CTA and "Related video" on the next Shorts |
| Weekly | `write_report` | — | Three best and three worst hooks with their patterns |

- Short metrics are read at D+2 after the Short goes public, like the long.
- Videos public for < 2 days: judge them only by `get_analytics` → `videos[].liveViews` (total since publish; pass
  `refresh_live: true`), never by `videos[].views` or the daily chart, which lag ±2 days. CTR, % watched and watch time
  come only from the Analytics figures at D+2/D+7. Never compare `liveViews` with period `views` in one table.
- Where notes go: one video → its `evaluasi` document (`put_document kind=evaluasi`); conclusions across videos →
  `write_report`. The `riset` document is for pre-production research only.
- Failed hook patterns go in the video's evaluasi document and in the weekly report; read the last report before
  writing new hooks.
- A new thumbnail or title for a published video is a proposal to the owner in your chat; the owner applies it in
  Studio. Do not change a live video yourself.

## Scope

- Applies to scripts and thumbnails that have **not** passed their gate. Approved work is not redone unless the owner asks.
- When an older channel rule conflicts (e.g. a mandatory logo intro after the hook, a logo in the thumbnail's
  bottom-right corner), report the conflict to the owner. Do not silently keep or drop either rule. Until the owner
  decides, the draft follows the new rule and the gate summary names the old element that was left out.
- Missing template items go through `propose_template_change`; the owner approves them.
