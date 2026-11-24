# Reading the algorithm during idea research

Owner decision, 24 Nov 2026. The full rule (Indonesian) is
`/mnt/data/Project/youtube/_bersama-youtube/ATURAN-RISET-ALGORITMA.md`. If this summary and the full rule disagree, the
full rule wins.

YouTube does not publish its algorithm. What you can read are its **traces**:
- which videos it pushed beyond their channel's size;
- what it places next to competitor videos;
- which formats win the Shorts feed in the niche;
- where your own channel's viewers come from.

Read these traces **before `score_idea`** for every idea. An idea without a "Pembacaan algoritma" section in
`opportunity_md` does not go to the Proposal gate.

Before you start:
- `get_idea` → check `idea_tools`. A tool you use that is not listed is still reported honestly in `tools_used`, and
  proposed via `propose_template_change`.
- Market: SI and Zeva `gl=ID`/`hl=id`; AC, LS and NF `gl=US`/`hl=en`, unless the channel's docs name another market.

Definitions:
- **Small channel:** < 100 k subscribers. **Mid-size:** 100 k–1 M.
- **High views:** above the median views of the top 20 `yt_analyze_keyword` results for the topic.
- **Outlier ratio:** the "performance vs channel" figure from `yt_analyze_video`.

## Long ideas: six signals

| # | Signal | Tools | Passes when |
|---|---|---|---|
| 1 | Outliers | `yt_analyze_keyword`, then `yt_analyze_video` on the top 3–5 (views/day ÷ that channel's median) | ≥ 2 videos from the last 12 months at ≥ 3×, at least one from a small or mid-size channel |
| 2 | Recommendation path | `yt_related_videos` on at least 2 top competitor videos | ≥ 5 same-niche, same-language videos with high views in the sidebar, where ours could appear |
| 3 | Saturation | `yt_analyze_keyword` (share of small channels, of videos < 12 months) | Not only giant channels with old videos, or a clear new angle |
| 4 | Packaging | Write all 3 title + thumbnail concepts (`hooks-thumbnails-schedule.md`) | All 3 written, and one has one focal point, ≤ 4 words, title ≤ 60 chars, a true promise |
| 5 | Retention | Short outline: 0–3 s promise, open question, payoff, competitor durations | A question that stays open until near the end; duration fits the tier proposed for the idea |
| 6 | Our audience | `get_analytics` on our own channel (top `traffic` RELATED_VIDEO/BROWSE, `avgViewSeconds`, % watched; CTR only if the owner supplied Studio figures) | Close to our videos the algorithm already pushed, or a stated reason to widen the pillar |

Also record, without a pass mark:
- timing — `yt_trending` and any news trigger, with the time window;
- competitor gaps — `yt_analyze_competitors` with `reference` = our channel.

**Signals 1, 4 and 6 must pass.** Otherwise defer the idea, or change the angle and read again.

- A failed idea may still be scored so it is recorded with "Putusan: tunda", but it does **not** go to the Proposal gate.
- **Owner exception.** The owner may ask, in explicit words in your chat, to submit a failed idea anyway. The section then
  says "submitted at the owner's request" and names the failed signal. Never raise a score to hide a failed signal.

How the reading feeds `score_idea` (1–10, higher is better; persaingan 10 = little competition):
- **permintaan** ← signals 1 and 2. No outlier means permintaan is at most 4.
- **persaingan** ← signal 3.
- **kecocokan** ← signal 6.

Record evidence videos with `add_idea_competitor`.

## Shorts: five signals

| # | Signal | Tools | Passes when |
|---|---|---|---|
| S1 | What wins the niche feed now | `yt_trending_shorts` (niche, 7–14 days) | ≥ 3 same-niche Shorts with high views in 14 days; format noted |
| S2 | Winning hook patterns | `yt_analyze_shorts` on 2–3 competitor channels (first 15 spoken words) | Our hook uses a proven pattern, not one logged as failed |
| S3 | Frame 0 | Describe the opening image | A strange or strong image that reads without text |
| S4 | Bridge to the long | Short ↔ long topic link (`yt_analyze_shorts` on our channel) | One question left for a long that is out or planned |
| S5 | Rewatch | Design the ending | The end loops into the start, or the last-second payoff invites a rewatch |

**S1, S3 and S4 must pass.** A derived Short reuses its parent's long reading and checks S1–S5 only.

- **S4 "out or planned":** a long that is public, has passed Proposal, or is submitted in the same batch. If the Short
  fits a long being submitted, make it a derived Short of that long.
- **Standalone Short scores:**
  - permintaan ← S1;
  - persaingan ← S2;
  - kecocokan ← signal 6 (our Shorts analytics);
  - produksi and evergreen as usual.
- **A long idea** fills the "Shorts" line with S1 for its niche and the frame-0 image of each planned derived Short.

## The `opportunity_md` section (fixed shape)

```
## Pembacaan algoritma (date, gl/hl)
Outlier: <title> — <channel, subs> — <ratio>× (at least 2)
Jalur rekomendasi: <competitor video> → <n> same-niche neighbours (2 example titles)
Kejenuhan: <small-channel share> / <share < 12 months>; new angle: …
Kemasan: 3 title + thumbnail concepts; chosen: …
Retensi: 0–3 s promise … / open question … / competitor durations …
Penonton kita: <our nearest video + traffic source + CTR>
Shorts: S1 … S2 … S3 … S4 … S5 …
Putusan: lolos / tunda / ubah sudut — one-sentence reason
```

## Cost

- About 6–8 tool calls per long idea. Use the cache; `fresh=true` only when the data must be new.
- Write key numbers only, in the shape above. Never paste raw tool output into documents.
- A reading may be reused for 14 days by ideas with the same main keyword.
- At D+2/D+7, compare the result with the reading. For example, "strong outliers but our CTR 2 %" means the packaging
  lost, not the topic.
- The weekly report names the signal that missed most often that week.

## Research stage (Riset, before requesting gate G3): read reports and analytics first

Owner decision, 25 Nov 2026. It applies to long videos and standalone Shorts. Derived Shorts start at Naskah, so they
are not covered.

**Before looking for sources:**
1. **Channel reports.** `list_channel_documents` (kind `laporan`) → `get_document` on the 2 latest.
2. **Evaluations of similar videos.** The `evaluasi` documents of up to 3 of our closest videos (same pillar or topic):
   `get_document` with `code` + kind `evaluasi`.
3. **Analytics.** `get_analytics` for the last 28 days: % watched, `avgViewSeconds` and `traffic` (with `code`, that
   video's traffic) of similar videos. CTR is not available through yt-pipeline; use it only if the owner supplied
   Studio figures.
   - Use `liveViews` for videos public under 2 days.
   - For a long video, also run `get_tier_performance` for the proposed tier.

**Write it down** in the `sumber` document, in a section "Pelajaran dari laporan & analitik" of at most about 10 lines:
- the findings used, with their numbers;
- how they change the research: which claims to dig into, what to cut, which viewer questions must be answered;
- which reports, evaluations and analytics period were read.

**No data yet** (new channel): write "belum ada laporan/analitik" and use the idea's algorithm reading as the comparison.

**Details:**
- **Standalone Short:** "similar videos" means our Shorts on the same topic plus the long it bridges to. No
  `get_tier_performance`.
- **Videos under 2 days old:** use `get_analytics` → `videos[].liveViews` with `refresh_live: true`. Record the figure as
  "sementara", never as evidence.
- **Writing to `sumber`:** `get_document` first, add this section, then `put_document` with `base_revision`. Never wipe
  existing content.
- **Tools:** until your template lists these tools, use them anyway and report them honestly in `tools_used` (owner
  decision, 25 Nov 2026). Propose the template change at the same time.
- **Exception:** skip this step only on the owner's explicit words, and quote them in the section.

**Limits:**
- Analytics sets the research's **priorities and angle**, not the truth of facts. Every number, date and name still
  needs ≥ 2 sources or a [PERLU CEK] tag.
- Never invent analytics numbers. Mark figures under 2 days old as "sementara".
- Record key numbers only; never paste reports or tool output into the document.
