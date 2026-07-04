# CranScan OCR language packs

Recognition language packs for [CranScan](https://github.com/samoylenkodmitry/cranscan) — a private, offline document scanner.

Each pack is a ZIP containing `rec.rten` (a PaddleOCR PP-OCRv5 recognition model converted to the [rten](https://github.com/robertknight/rten) runtime) and `dict.txt`. Install in-app via **Settings → Text recognition** (one-tap download or manual import).

Models originate from [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) (Apache-2.0), ONNX exports via [monkt/paddleocr-onnx](https://huggingface.co/monkt/paddleocr-onnx).
