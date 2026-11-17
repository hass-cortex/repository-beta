Includes every change from 0.6.2, whose app image was never published.

## Fixes

- **deps**: update rusqlite to 0.42 for RUSTSEC-2026-0459 (bundled SQLite CVE-2026-119375)
- **docker**: build with Rust 1.93 (rusqlite 0.42 requires rustc 1.89)

## Other Changes

- **deps**: update transcribe-cpp to 0.4.2 — Parakeet, Canary and Canary-Qwen no longer feed the NeMo padding frame to the encoder, so their transcripts may differ slightly from 0.6.1; ggml-cpu skips x86 kernel variants whose register state the OS has not enabled
- **deps**: update semver-compatible crates
