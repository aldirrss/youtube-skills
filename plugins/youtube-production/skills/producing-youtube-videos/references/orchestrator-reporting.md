# Reporting to the Orchestrator

The Orchestrator is a separate Claude session that owns two shared resources: the Kaggle GPU quota and R2 storage.
The full protocol lives in the shared folder (`_bersama-youtube/PROTOKOL-ORCHESTRATOR.md`); this file is the
working summary. Keywords stay in Indonesian because every session uses them verbatim.

## Addressing

- `SendMessage` to `"Orchestrator"`. If delivery fails or is ambiguous, run `ListAgents` and use the listed name/ref.
- **First line = the keyword** (`GPU-MINTA`, `R2-SELESAI`, …) so the preview is readable.
- A message that is held, refused or unanswered is **not** permission. Silence never means yes.
- One topic per message. Corrections are sent as a new message, not by editing assumptions.

## GPU messages

| Keyword | When | Must contain |
|---|---|---|
| `GPU-MINTA` | Before any push | channel, kernel ref, video code, purpose (`produksi`/`ulang`/`uji`/`audisi`), utterances, **target audio seconds**, estimated minutes, the owner's exact words if `uji`/`audisi` |
| `GPU-MULAI` | Right after a successful push | kernel ref **and version number**, push time UTC, whether it is RUNNING or QUEUED |
| `GPU-SELESAI` | After COMPLETE / ERROR / cancel | RUNNING and end time UTC, GPU seconds from the kernel log, accelerator, utterances, attempts, audio seconds, failed clips **after local QC** (with reasons), status |

Status changes after the push (still QUEUED after a long wait, QUEUED → RUNNING) are sent as another
`GPU-MULAI` whose first line ends with `(update)`, with the version and the time of the change.

Wait for `GPU-IZIN` before pushing. `GPU-TUNDA` means wait; it includes the reason (another kernel running,
quota, owner decision needed).

Example:

```
GPU-MINTA
Channel: kampung-tekotek · kernel zetrynai/kt-suara-karakter (new version)
Code: KT-0001 · purpose: produksi · 61 utterances (hanya: 53 remaining + 8 redo)
Target audio: ±170 s → estimate 5 + 0.12 × 170 ≈ 25 min (T4 x2)
Owner approval: voices approved in this chat ("Cocok, lanjut")
```

## R2 messages

| Keyword | When | Must contain |
|---|---|---|
| `R2-MINTA` | Before a file > 100 MB or a batch > 300 MB | code, file list with size and kind, which `replace_asset_id` each replaces (if any) |
| `R2-SELESAI` | After `complete_upload` (small files may be batched daily) | asset_id, filename, bytes; for replacements the old → new size |
| `R2-IZIN-UNGGAH` | As soon as the owner approves the **Izin unggah** gate | code; the Orchestrator then proposes the cleanup. Do **not** call `propose_asset_cleanup` yourself |

The Orchestrator answers `R2-IZIN` or `R2-TUNDA`. After the owner processes a cleanup, the Orchestrator reports
which assets were deleted; record them in the video checklist.

## When the Orchestrator is unreachable

Do not push and do not upload large files. Tell the owner in your own chat. Only the owner's explicit words in
that chat allow you to proceed; quote them in the next message to the Orchestrator.
