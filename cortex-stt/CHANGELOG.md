## 0.3.0 — single transcribe.cpp runtime

The engine layer is rebuilt on [transcribe.cpp](https://github.com/handy-computer/transcribe.cpp) (GGUF/ggml): one runtime for every model family — Whisper, Parakeet, SenseVoice, Qwen3-ASR, Canary, Moonshine, and more — with real incremental streaming over a single WebSocket endpoint.

### ⚠️ Breaking changes — read before updating

- **All previously downloaded models are removed on first start.** 0.2.x model files (`.bin` whisper / ONNX directories) cannot be loaded by the new runtime and are deleted automatically. **Re-download your models from the catalog after updating** (Admin UI → Models).
- **Model ids changed** to upstream catalog slugs (e.g. `whisper-tiny-int8` → `whisper-tiny`, `sense-voice-int8` → `SenseVoiceSmall`). Home Assistant STT entities are rebuilt under the new ids — voice pipelines must re-select their STT entity, and anything wrapping an old entity (e.g. **STT Corrector**) will raise a repair issue; use its fix flow to re-point at the new entity.
- **A stored default model that no longer exists** leaves `/health` reporting `starting` until you download a model and set a new default.
- **SSE transcription variant removed** — streaming is WebSocket-only (`GET /api/transcribe/stream`). Sync JSON and async jobs are unchanged. API clients using the old `Accept: text/event-stream` variant of `POST /api/transcribe` must migrate.
- **Wire format changes**: `/api/models` now carries quant info (`quants[]`, `default_quant`, `downloaded_quant`); history `segments` is structured (no `segments_json`).

**Update together with** the [cortex-stt integration ≥ 0.4.0](https://github.com/hass-cortex/cortex-stt/releases/tag/0.4.0) (WS streaming + new model wire format) and, if you use it, [stt-corrector ≥ 0.7.0](https://github.com/hass-cortex/stt-corrector/releases/tag/0.7.0).

### Highlights

- **Multi-model on one runtime**: full Handy catalog (60+ models, 3–6 quants each), pick a quant at download time, one quant per model on disk
- **Real streaming**: feed/finalize incremental decoding with partial transcripts; non-streaming models transparently fall back to buffered mode (same wire contract)
- **Capture-device attribution + audio quality metrics**: history records which Assist satellite recorded each utterance (with the HA integration) plus RMS/peak/clipping levels — find the microphone that transcribes poorly
- **Live admin UI**: engine load state (lazy loads, idle offloads) and history stream over SSE; per-device history filters
- **History facets, invalid-filter 400s, retention unchanged** (records + audio survive the update; audio now stored as Ogg Opus for new records)

**Full Changelog**: https://github.com/hass-cortex/app-cortex-stt/compare/0.2.0...0.3.0
