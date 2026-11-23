# R2 assets

All files live in yt-pipeline's R2 storage. Budget and per-channel quota are set by the owner; the Orchestrator
grants large uploads and proposes cleanups. Only the owner deletes, in the web-app.

## Upload flow

1. `create_upload` (with `replace_asset_id` when the file is a new version of an existing asset).
2. Run the returned `curl` exactly as given.
3. `complete_upload` with the file's sha256.
4. Send `R2-SELESAI` with asset_id, filename and bytes.

Large files (> 100 MB) or batches (> 300 MB) need `R2-IZIN` first (`R2-MINTA`).

## New version = replace, never stack

| Situation | Do |
|---|---|
| Re-render of a preview, final, Short, thumbnail or voice sample | `create_upload(..., replace_asset_id: "<old id>")` — ID stays, old file is deleted automatically, gate review links keep working |
| Owner explicitly wants the old version kept for comparison | Upload as new and say so in `R2-SELESAI` |
| A rename (e.g. to fix `short01` → `short-01`) | Same as a new version: replace, sha256 unchanged |

Uploading a revision as a new asset leaves the old one in R2 until the next cleanup; it happened with a 94 MB
preview.

## What is kept vs cleaned

Kept: the current final long, all Shorts, all thumbnails, `.srt`, documents, shared assets in use.
Cleanup candidates after **Izin unggah** is approved: previews, voice samples, superseded renders.

After the owner approves Izin unggah, send `R2-IZIN-UNGGAH`; the Orchestrator verifies the gate, lists the assets and
calls `propose_asset_cleanup`. Never propose a cleanup yourself (it creates duplicates). Shared-folder assets
(e.g. audition files) cannot be proposed; the owner deletes them manually.

## Naming

`<CODE>_<kind>[-<variant>]_<version>.<ext>`, for example `SI-0005_long_16x9_v1.mp4`, `AC-0002_short-03.mp4`,
`LS-0002_pratinjau-v1_ringan.mp4`, `KT-0001_contoh-suara_long-dialog.mp3`. The `short-NN` part is mandatory for Shorts.

## Short cuts

Each Short has two final files: the short cut (`<CODE>_short-NN.mp4`, `kind: "short"`) and the long cut
(`<CODE>_short-NN_panjang.mp4`, `kind: "lain"`). Count both in `R2-MINTA`. See `final-render.md` for why the kinds differ.
