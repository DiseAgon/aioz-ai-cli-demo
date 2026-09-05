# AIOZ AI operator CLI — Linux amd64 demo

Public **download + version-check** host for the operator CLI. This is not the source repository.

## Demo target

| | |
|---|---|
| OS | **Linux** |
| Arch | **amd64** (`x86_64`) |
| Package | **`ai-cli` only** |

macOS, Windows, and other architectures are **not** part of this demo.

The node runtime and `keytool` are **bundled inside `ai-cli`**. They are extracted on first use. Do not download or run those files on their own.

## Install

```bash
curl -fsSL https://github.com/DiseAgon/aioz-ai-cli-demo/releases/latest/download/install.sh | bash
source ~/.bashrc
ai-cli version
```

Installs `~/.local/bin/ai-cli` (not the current working directory). This GitHub repo keeps **only the latest** release. Older tags are removed.

## Demo features

| Command | What it demos |
|---|---|
| `ai-cli keytool new --save-priv-key priv.json` | Create a node credential file |
| `ai-cli start --priv-key-file priv.json` | Start this wallet's node (Ctrl+C stops this wallet) |
| `ai-cli status --priv-key-file priv.json` / `ai-cli logs --priv-key-file priv.json` | Process and `node.log` for this wallet |
| `ai-cli stats --priv-key-file priv.json` | Runtime node info |
| `ai-cli storage show --priv-key-file priv.json` | Cap and used (GB/MB; must be greater than 2 GB) |
| `ai-cli reward balance` / `ai-cli reward withdraw` `--priv-key-file priv.json` | Rewards (minimum withdraw 0.01 AIOZ) |
| `ai-cli update` | Check this GitHub latest |
| `ai-cli doctor --priv-key-file priv.json` | os, disk, runtime, GPU, wallet, log (not keytool) |

Without `--home`, data for a wallet is `~/.local/share/aioz/ai-nodes/<uuid>/`. Wallet is `identity/address`, not the folder name.

## Release assets

Each latest release contains:

- `ai-cli` — Linux amd64 operator binary (runtime + keytool embedded)
- `install.sh` — downloads `ai-cli` onto `~/.local/bin` and persists PATH
- `manifest.json` — version/commit + SHA-256 + Ed25519 `sig` for `ai-cli`

Operators do not need a `.env`. Hub and the version-check URL are baked into the binary. `install.sh` verifies the manifest signature before installing.
