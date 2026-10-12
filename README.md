# 2Drained Updates

Public distribution repository for 2Drained launcher and game update metadata.

Source code remains in private repositories. This repository contains only public update manifests and distributable update payloads.

## Structure

- `launcher/` - 2Drained-Launcher update feed
- `games/cosmostellaria/` - CosmoStellaria game manifest
- `games/factory-defense/` - Factory Defense game manifest

## Game distribution types

The V0.2 launcher supports two package types.

### Tauri / NSIS

Use this for games that already publish a Tauri updater feed.

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
    "maxDownloadBytes": 21474836480,
    "maxUnpackedBytes": 42949672960,
    "maxFiles": 100000
  },
  "notes": "Full launcher-managed ZIP distribution."
}
```

ZIP packages require SHA-256 verification. The launcher rejects unsafe archive paths, symbolic links, special files, oversized downloads, excessive unpacked size, excessive file counts and unapproved download hosts. ZIP installs are staged before activation.

Do not commit signing private keys, passwords, tokens, or other secrets to this repository.
