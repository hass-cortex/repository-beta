# repository-beta

HA app catalog for hass-cortex (**beta channel**). Metadata-only — no Dockerfiles, no `run.sh`, no `rootfs/`.

This catalog is a **superset** of the stable `repository` repo: it receives every stable release plus pre-releases. Install this catalog in HA to get early access; install [`repository`](https://github.com/hass-cortex/repository) for production-only.

## Structure

Each top-level subdirectory with a `config.yaml` is an HA app. HA Supervisor auto-discovers them. App source code and build logic live in separate `app-*` source repos (see workspace root `CLAUDE.md`).

```
repository-beta/
├── repository.yaml           # Supervisor catalog metadata
├── .apps.yml                 # repository-updater config (channel: beta)
├── .github/workflows/
│   └── repository-updater.yaml
├── cortex-stt/               # mirrors stable + pre-releases
│   ├── config.yaml           # image: ghcr.io/hass-cortex/cortex_stt/{arch}
│   ├── DOCS.md
│   ├── CHANGELOG.md          # auto-generated
│   ├── icon.png
│   ├── logo.png
│   └── translations/en.yaml
```

## Updates

Source repos (`hass-cortex/app-*`) dispatch `repository_dispatch` events on every release (stable and pre-release). The hassio-addons app-deploy workflow's `publish-beta` job fires for both `stable` and `beta` environments, so this catalog stays a superset of stable. `repository-updater.yaml` runs `hassio-addons/repository-updater@v2.0.1` to refresh the catalog.

## DO NOT Edit Manually

Files like `config.yaml` version, `CHANGELOG.md`, and `DOCS.md` are overwritten by the updater. To change an app, edit its **source repo** and cut a new release. To add a new app, add its entry to `.apps.yml` and create its subdirectory here.
