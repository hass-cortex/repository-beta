## Features

- **Upstream model updates**: the app notices when upstream republishes an installed model file and can re-download it in place. The old file keeps serving until the new one verifies. (#43)
- **transcribe.cpp 0.3.1**. Since 0.3.0, a decode that starts repeating itself stops early, and the app returns the transcript up to that point (`truncated: true`) instead of failing the request. 0.3.1 adds a backend allowlist. (#53)
- **Neutral gray theme** for the admin UI surfaces and text. (#45)

## Fixes

- **Model downloads no longer break when upstream re-uploads a file.** Each catalog model is now pinned to a Hugging Face commit, and its URL, SHA-256 and size all come from that commit. Earlier versions downloaded from `main` and rejected files that upstream had re-hashed in place. On 0.4.3 and older, `canary-180m-flash` and `canary-1b-flash` currently fail to install for this reason. Starting with this release, upstream changes can't break an existing version this way. (#54)
- **Download failures stay visible.** A failed download used to clear after two seconds, and the row went back to "available" without saying why. The reason now stays under the row until you retry or dismiss it. `/api/models` reports it as `status: "error"` with a new `download_error` field. (#54)
- Today's counters reset at local midnight instead of UTC midnight.
- The clip player resets playback and the reference transcript when the clip changes.
- Data series have their own color, so red only means an error. (#46)

## Other Changes

- Corrected 42 model sizes in the catalog that had been copied from an older upstream commit.
- Dependency updates: react-router 8, TypeScript 7, transcribe-cpp 0.2.4 → 0.3.1, and others. (#42, #47–#50)
- Dependabot now checks quarterly and takes only major updates.

**Full Changelog**: https://github.com/hass-cortex/app-cortex-stt/compare/0.4.3...0.5.0
