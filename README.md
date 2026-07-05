# CranScan model packs

Downloadable model packs for [CranScan](https://github.com/samoylenkodmitry/cranscan) — a private, offline document scanner: OCR language packs (`packs-v1`) and Receipt-AI vision models (`ai-v1`).

Each pack is a ZIP containing `rec.rten` (a PaddleOCR recognition model converted to the [rten](https://github.com/robertknight/rten) runtime) and `dict.txt`. Install in-app via **Settings → Text recognition** (one-tap download or manual import).

## Packs (release `packs-v1`)

| Pack | Model | Size | Scripts / languages |
| --- | --- | --- | --- |
| `cyrillic.zip` | cyrillic_PP-OCRv5_mobile_rec | 7 MB | Cyrillic incl. **Serbian** (ЂЈЉЊЋЏ), Bulgarian, Russian, Ukrainian, Belarusian + Latin digits |
| `latin.zip` | latin_PP-OCRv5_mobile_rec | 7 MB | Latin (Western/Central European) |
| `greek.zip` | el_PP-OCRv5_mobile_rec | 7 MB | Greek |
| `arabic.zip` | arabic_PP-OCRv5_mobile_rec | 8 MB | Arabic |
| `hindi.zip` | devanagari_PP-OCRv5_mobile_rec | 8 MB | Devanagari |
| `korean.zip` | korean_PP-OCRv5_mobile_rec | 8 MB | Korean |
| `thai.zip` | th_PP-OCRv5_mobile_rec | 7 MB | Thai |
| `tamil.zip` | ta_PP-OCRv5_mobile_rec | 8 MB | Tamil |
| `telugu.zip` | te_PP-OCRv5_mobile_rec | 8 MB | Telugu |
| `chinese.zip` | **PP-OCRv5_server_rec** | 76 MB | Chinese (simpl.+trad.), English, Japanese, Pinyin — server tier |
| `multi-small.zip` | **PP-OCRv6_small_rec** | 19 MB | Chinese, English, Japanese, Greek, Latin-script languages — beats PP-OCRv5 server on most benchmarks at a quarter of the size |
| `multi-hq.zip` | **PP-OCRv6_medium_rec** | 67 MB | Chinese, English, Japanese, Greek, Latin-script languages — highest accuracy available (+5.1% over PP-OCRv5 server) |

Note: no server/large-tier Cyrillic recognition model exists in the PaddleOCR family yet (PP-OCRv6 covers 50 languages but not Cyrillic); `cyrillic.zip` (mobile tier) is the best available for Serbian/Bulgarian and is the only pack whose dictionary includes the Serbian-specific letters ЂЈЉЊЋЏ.

## Receipt AI packs (release `ai-v1`)

Local vision-language models that read a receipt **photo** directly — merchant, date, total, currency, line items and a spending category — far more robustly than OCR + heuristics. Used by the CranScan **desktop** app (Settings → Receipt AI); everything runs on-device via a bundled [llama.cpp](https://github.com/ggml-org/llama.cpp) `llama-server` (build `b9873`, CPU).

| Pack | Model | Download | RAM while reading | Notes |
| --- | --- | --- | --- | --- |
| `qwen3vl-4b` | Qwen3-VL-4B-Instruct **Q4_K_M** | ~3.3 GB (2 shards + mmproj) | ~6.7 GB | Best quality; recommended |
| `qwen3vl-2b` | Qwen3-VL-2B-Instruct **Q8_0** | ~2.3 GB | ~4.5 GB | Faster, good on clean receipts |

Each pack is described by a `<id>.manifest.json` release asset:

```json
{
  "id": "qwen3vl-4b",
  "ram_hint_mb": 6700,
  "total_bytes": 3333463168,
  "model": "Qwen3-VL-4B-Instruct-Q4_K_M-00001-of-00002.gguf",
  "mmproj": "mmproj-Qwen3-VL-4B-Instruct-F16.gguf",
  "files":   [ { "name": "…", "bytes": 123, "sha256": "…" } ],
  "servers": { "linux-x86_64": { "name": "…tar.gz", "bytes": 123, "sha256": "…", "bin": "llama-server" } }
}
```

- `files` lists the GGUF shards + multimodal projector (every file sha256-pinned; models over 2 GB are split with `llama-gguf-split`, `llama-server` loads the first shard and finds the rest).
- `servers` maps a platform (`linux-x86_64`, `macos-arm64`, `macos-x86_64`, `windows-x86_64`) to a minimal, flat `llama-server` archive (binary + required shared libraries, repacked from the official llama.cpp `b9873` release).
- The app downloads `files` + its platform's server archive, verifies every sha256, unpacks the server and talks to it over localhost — nothing leaves the device.

Models are the official [Qwen3-VL](https://huggingface.co/Qwen) instruct releases (Apache-2.0), GGUF quantizations from the ggml-org/unsloth conversions; llama.cpp is MIT. Packs are built and uploaded by [`scripts/build-ai-packs.sh`](https://github.com/samoylenkodmitry/cranscan/blob/main/scripts/build-ai-packs.sh) in the app repo.

## Detection model

`paddle-det-v6-medium.rten` — **PP-OCRv6_medium_det** (62 MB), a server-grade text detector (+4.6% Hmean over PP-OCRv5 server det). Not an installable pack; intended to be bundled with the app in place of the default PP-OCRv6 small detector for far better line detection on crumpled/hand-held receipts.

## Sources

Models originate from [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) (Apache-2.0). ONNX exports: official [PaddlePaddle Hugging Face `*_onnx` repos](https://huggingface.co/PaddlePaddle) (`cyrillic_PP-OCRv5_mobile_rec_onnx`, `PP-OCRv6_medium_rec_onnx`, `PP-OCRv6_small_rec_onnx`, `PP-OCRv6_medium_det_onnx`) and [monkt/paddleocr-onnx](https://huggingface.co/monkt/paddleocr-onnx) (the PP-OCRv5 mobile language packs and PP-OCRv5 server chinese). Converted with `rten-convert`; dictionaries extracted from each model's `inference.yml` (`PostProcess.character_dict`). The `cyrillic` ONNX ships as opset 7 and must be upgraded (e.g. `onnx.version_converter.convert_version(model, 13)`) before `rten-convert`, otherwise Slice ops fail at runtime.
