# GPU and R2 requests (no Orchestrator)

Owner decision, 27 Nov 2026: **there is no Orchestrator session.** In the owner's words: "disini tidak ada
orchestrator … kalo mau pengajuan gpu tinggal minta antrean nanti saya izinkan". The old keywords (`GPU-MINTA`,
`GPU-IZIN`, `GPU-MULAI`, `GPU-SELESAI`, `GPU-TUNDA`, `TTS-LOKAL-SELESAI`, `HALO`, `R2-MINTA`, `R2-SELESAI`,
`R2-IZIN-UNGGAH`) are retired. Never `SendMessage` to "Orchestrator", never wait for one, and never tell the owner a
step goes "through the Orchestrator". Older notes, templates and checklists that still say so are outdated.

The shared folder that used to be `_bersama-youtube/` and then `orchestrator/` is now `manager/` at the repo root
(`../manager/...` from a channel folder). It only holds shared rules, tools and the channel template.

## Kaggle GPU: ask the owner for a queue slot

1. `get_gpu_usage` for the channel's remaining weekly hours.
2. Ask the owner **in your own chat**, in one message:

   ```
   Minta antrean GPU Kaggle
   Kernel: zetrynai/<xx>-produksi-suara · video <CODE> · purpose: produksi / ulang / uji / audisi
   Isi: <n> ucapan, target audio ±<s> dtk → perkiraan ±<m> mnt (T4 x2)
   Sisa jatah <channel> minggu ini: <h> jam
   ```

   Tests and auditions also need the owner's explicit words; quote them.
3. Push only after the owner says yes. A yes covers that request only; a different batch or changed script is a new
   request.
4. `reserve_tool kaggle-zetrynai` for the estimated minutes. Kaggle runs one kernel at a time across all channels;
   if the tool is already reserved, tell the owner you are waiting and push when it is released.
5. Push, then watch the exact version (`kaggle-tts.md`).
6. When the run ends (COMPLETE, ERROR or cancelled): `release_tool kaggle-zetrynai`, then record it yourself with
   `log_gpu_session` / `update_gpu_session` (GPU minutes from the kernel log, utterances, audio seconds, failed
   clips after local QC). Tell the owner the result in one short message.

`kaggle-gpu-ledger.csv` is no longer written; yt-pipeline is the only record.

## Local GPU (`gpu-lock`, `tts-lokal`)

No approval and no report. Use `gpu-lock` as usual and note the run in the video's checklist.

## R2

Upload directly (`r2-assets.md`). For a file over 100 MB or a batch over 300 MB, check `get_storage_usage` first and
ask the owner only if the channel quota would be exceeded. After the owner approves **Izin unggah**, call
`propose_asset_cleanup` yourself; the owner deletes in the web-app.

## When the owner has not answered

Do not push and do not exceed the R2 quota. Continue with work that needs neither, and ask once more later.
