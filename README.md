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

This GitHub repo keeps **only the latest** release. Older tags are removed.

## Demo features

| Command | What it demos |
|---|---|
| `ai-cli keytool new --save-priv-key priv.json` | Create a node credential file |
| `ai-cli start --priv-key-file priv.json` | Start the node in the foreground (Ctrl+C stops this home) |
| `ai-cli status` / `ai-cli logs` | Process and logs for `--home` |
| `ai-cli stats` | Node statistics |
| `ai-cli storage show` / `ai-cli storage limit 10` | Storage cap (must be **greater than 2 GB**) |
| `ai-cli reward balance` / `ai-cli reward withdraw` | Reward balance and withdraw to a MetaMask `0x` address (minimum 0.01 AIOZ) |
| `ai-cli update` | Check this GitHub latest for a newer CLI |
| `ai-cli doctor` | Local health (workspace, disk, runtime, GPU) |

## Release assets

Each latest release contains:

- `ai-cli` — Linux amd64 operator binary (runtime + keytool embedded)
- `install.sh` — downloads `ai-cli` onto `~/.local/bin` and persists PATH
- `manifest.json` — version/commit + SHA-256 for `ai-cli` (used by install and `ai-cli update`)
