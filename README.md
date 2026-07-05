# CranScan OCR language packs

Recognition language packs for [CranScan](https://github.com/samoylenkodmitry/cranscan) — a private, offline document scanner.

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

## Detection model

`paddle-det-v6-medium.rten` — **PP-OCRv6_medium_det** (62 MB), a server-grade text detector (+4.6% Hmean over PP-OCRv5 server det). Not an installable pack; intended to be bundled with the app in place of the default PP-OCRv6 small detector for far better line detection on crumpled/hand-held receipts.

## Sources

Models originate from [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) (Apache-2.0). ONNX exports: official [PaddlePaddle Hugging Face `*_onnx` repos](https://huggingface.co/PaddlePaddle) (`cyrillic_PP-OCRv5_mobile_rec_onnx`, `PP-OCRv6_medium_rec_onnx`, `PP-OCRv6_small_rec_onnx`, `PP-OCRv6_medium_det_onnx`) and [monkt/paddleocr-onnx](https://huggingface.co/monkt/paddleocr-onnx) (the PP-OCRv5 mobile language packs and PP-OCRv5 server chinese). Converted with `rten-convert`; dictionaries extracted from each model's `inference.yml` (`PostProcess.character_dict`). The `cyrillic` ONNX ships as opset 7 and must be upgraded (e.g. `onnx.version_converter.convert_version(model, 13)`) before `rten-convert`, otherwise Slice ops fail at runtime.
