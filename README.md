# 2Drained Updates

Public distribution repository for 2Drained launcher and game update metadata.

Source code remains in private repositories. This repository contains only public update manifests and distributable update payloads.

## Structure

- `launcher/` - 2Drained-Launcher update feed
- `games/cosmostellaria/` - CosmoStellaria game manifest
- `games/factory-defense/` - Factory Defense game manifest

## Trust model

The public manifests describe releases, but they are **not** the security trust anchor.

For every installable game, the launcher backend pins the trusted manifest location, install directory, allowed executable names and package-signing public key. A modified web UI or modified remote manifest therefore cannot replace these values.

Every downloaded game package must pass its package-signature verification before it can be installed. ZIP packages additionally require a SHA-256 digest.

## Game distribution types

### Tauri / NSIS

Use this for games that already publish a Tauri updater feed. The updater feed provides the package URL and Tauri/Minisign signature. The corresponding public key is pinned in the launcher backend.

```json
{
  "id": "example-game",
  "name": "Example Game",
  "channel": "stable",
  "currentVersion": "1.0.0",
  "executable": "ExampleGame.exe",
  "installDirectory": "ExampleGame",
  "status": "installable",
  "installer": {
    "type": "tauri-nsis",
    "manifestUrl": "https://approved-update-host.example/latest.json",
    "silent": true
  }
}
```

### Full ZIP package

Use this for games that do not need their own installer. The ZIP should contain the game files at archive root, or inside exactly one top-level folder. The expected executable must exist at the relative `executable` path.

```json
{
  "id": "example-game",
  "name": "Example Game",
  "channel": "stable",
  "currentVersion": "1.0.0",
  "executable": "ExampleGame.exe",
  "installDirectory": "ExampleGame",
  "status": "installable",
  "installer": {
    "type": "zip",
    "url": "https://github.com/DrainedSloth/example-game/releases/download/v1.0.0/example-game-v1.0.0-full.zip",
    "sha256": "REPLACE_WITH_64_CHARACTER_SHA256",
    "signature": "BASE64_ENCODED_MINISIGN_SIGNATURE",
    "maxDownloadBytes": 21474836480,
    "maxUnpackedBytes": 42949672960,
    "maxFiles": 100000
  },
  "notes": "Full launcher-managed ZIP distribution."
}
```

The ZIP package public key must be pinned in the launcher backend before the manifest is treated as installable.

## ZIP safety controls

The launcher rejects ZIP packages with unsafe archive paths, Windows reserved device names, symbolic links, special files, oversized downloads, excessive unpacked size, excessive file counts, invalid SHA-256 hashes, invalid package signatures, or unapproved download hosts.

ZIP files are extracted into a staging directory first. Existing launcher-managed installations are replaced only after validation. Launcher-managed ZIP installations contain a `.2drained-managed.json` ownership marker; ZIP update, repair and uninstall refuse directories without a matching marker.

Do not commit signing private keys, passwords, tokens, or other secrets to this repository.
