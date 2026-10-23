## Breaking Changes

- **A request names a Variant: one model at one quant.** `model=` on `/api/transcribe`, the stream `start` message and `/api/engine/load|unload` takes a key such as `SenseVoiceSmall:Q8_0`. A bare model id still works while that model has a single quant installed; once it has several, the request is refused with 409 `VARIANT_REQUIRED` and the message lists the keys to use. The app never picks a quant for the caller. If something other than the Home Assistant integration calls the app, give it the full key before you install a second quant of the same model. The response's `model` is the key that served.
- **The default model is gone; Pre-load on startup replaces it.** No request ever fell back to the default model, so the only thing it did was pick what to load at startup. Settings now has **Pre-load on startup**, which is one Variant or none. A default model that was set to pre-load becomes its earliest-installed Variant on first start. `PUT /api/engine/default` and `default_model`/`preload_default_model` in `/api/settings` are replaced by `preload`; the CLI's `--default-model`/`--preload-model` become `--preload <key>`.
- **Model files move into one folder per model**, each with a small `.json` file beside it saying which model and quant it is. This happens on first start. After it, 0.5.x no longer finds the files, so going back to 0.5.x means downloading them again.
- **`moss-transcribe-diarize` is now `MOSS-Transcribe-Diarize`**, following upstream. If you installed it, its Home Assistant entity is recreated under the new name.
- **`/api/models` wire changes:** each model has a `variants[]` list, and `downloaded_quant`, `updating`, `update_available` and `download_error` moved into it. `capabilities.timestamps` is a bool. `recommended_rank`, `speed_score` and `accuracy_score` are removed, and `recommended` now follows upstream's curated list. `/api/engine` adds `loaded_variants`, a list of keys.
- **Integration 0.5.0 requires this release.** Update the app first, then the integration. An older integration keeps working against this app for every model that has a single quant installed.

## Features

- **Several quants of one model, installed and in use at once.** Install Q4_K_M beside Q8_0, point one Assist pipeline at each, and compare them in an evaluation run. Downloading a quant adds it and never removes another; each one updates, loads and uninstalls on its own. With integration 0.5.0, Home Assistant shows each quant as its own device.
- **A redesigned Models page.** Each installed model is a card with a row per quant. Each row shows the quant's size, whether it is loaded, and its own median latency and run count from your history. **Add quant** lists the quants you can still install. On a phone the rows become two lines, and the menus open as sheets from the bottom of the screen.
- **Try it on Transcribe**, in a quant's menu, opens the Transcribe page with that quant selected.
- **Catalog sync with upstream schema 2**, which adds granite-speech-5.0 (two sizes), parakeet-ultra, orukeet and multitalker-parakeet-streaming.
- **22 models now download Q8_0 by default** instead of Q5_K_M. This applies only to new downloads; installed quants stay as they are. Voxtral-Small-24B's default download grows from 17.1 to 25.8 GB.
- **transcribe.cpp 0.4.1.** 0.4.0 adds voice activity detection, language identification and speaker diarization APIs and moves to ggml 0.26.0. For Fun-ASR-MLT-Nano-2512, 0.4.1 turns inverse text normalization on by default, so numbers and casing come out written rather than spelled out.

## Fixes

- `/health` reports `ok` once the app is listening. Before, a fresh install with nothing downloaded reported `starting` indefinitely.
- Model weights are copied into memory again, not memory-mapped (transcribe.cpp 0.4.1 changed the default). With mapped weights, an evaluation's memory reading came out too low, an idle model could lose its weights to the system before the next voice command, and overwriting a custom model file in place could crash the app.
- Updating a model can no longer leave the old file's weights serving. A cancel that arrives after the new file has verified no longer stops the install. A failed update no longer turns the row into an error once you uninstall the model.
- A catalog sync never moves a model back to older files, and language tags are reduced to their base language (`zh-TW` → `zh`), which is what Home Assistant matches on.
- The evaluation dialog shows whether each candidate declares the run's language, and `asr-cli list` no longer misaligns its columns on long model ids.
- A custom model file whose name contains `:` is skipped, because the name would read as a model and a quant.

## Other Changes

- Dependency updates for rustls, rustls-webpki and webpki-root-certs security advisories.
- The domain vocabulary moved from `CONTEXT.md` to `GLOSSARY.md`. ADR 0007 records the Variant design.

**Full Changelog**: https://github.com/hass-cortex/app-cortex-stt/compare/0.5.0...0.6.0
