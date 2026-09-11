## AIOZ AI CLI

Linux amd64, Windows amd64, macOS Apple Silicon (arm64), and macOS Intel (amd64) demo of the AIOZ AI operator CLI. This GitHub repository is **download + version-check only**. It is not the source tree. Only the **latest** release is kept.

`ai-cli` runs an AIOZ AI node on your machine, takes AI tasks, and earns AIOZ rewards. The node runtime and keytool are **bundled inside the binary** and extracted on first use.

## Requirements

- Windows 10 64-bit (amd64) or later
- Ubuntu 20.04 64-bit (amd64) or later
- macOS 12+ 64-bit, Apple Silicon (arm64) or Intel (amd64)

## Output

Command **stdout is indented JSON**. There is no `--json` flag. **Help is human.** Failures print a text line on **stderr** (not JSON).

Windows PowerShell: type `.\ai-cli.exe` (the `.\` is required). Linux and macOS: `./ai-cli` or `ai-cli` if it is on `PATH`.

## Install

### Windows

Work in **your** profile folder. PowerShell as that user, not Administrator:

```powershell
cd $env:USERPROFILE
irm https://github.com/DiseAgon/os-pack-dl/releases/latest/download/install.ps1 | iex
```

Or download the zip:

```powershell
cd $env:USERPROFILE
curl.exe -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-windows-amd64-0.24.zip
Expand-Archive -Path aioz-ai-cli-windows-amd64-0.24.zip -DestinationPath .
ren aioz-ai-cli-windows-amd64.exe ai-cli.exe
.\ai-cli.exe version
```

`--save-priv-key privkey.json` writes into the current folder. `Access is denied` means that folder is not yours — `cd $env:USERPROFILE` and retry. Data is under `%LOCALAPPDATA%\AIOZ\ai-cli\`. The first `start` may show a Windows Firewall prompt; allow it for private networks.

### Linux

```bash
curl -fsSL https://github.com/DiseAgon/os-pack-dl/releases/latest/download/install.sh | bash
```

Or the archive:

```bash
curl -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-linux-amd64-0.24.tar.gz
tar -xzf aioz-ai-cli-linux-amd64-0.24.tar.gz
mv aioz-ai-cli-linux-amd64 ai-cli
./ai-cli version
```

### macOS

`install.sh` picks Apple Silicon vs Intel from `uname -m`:

```bash
curl -fsSL https://github.com/DiseAgon/os-pack-dl/releases/latest/download/install.sh | bash
```

| `uname -m` | Chip | Archive | Inner file |
|------------|------|---------|------------|
| `arm64` | Apple Silicon (M1–M4) | `aioz-ai-cli-darwin-arm64-0.24.tar.gz` | `aioz-ai-cli-darwin-arm64` |
| `x86_64` | Intel | `aioz-ai-cli-darwin-amd64-0.24.tar.gz` | `aioz-ai-cli-darwin-amd64` |

Apple Silicon:

```bash
curl -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-darwin-arm64-0.24.tar.gz
tar -xzf aioz-ai-cli-darwin-arm64-0.24.tar.gz
mv aioz-ai-cli-darwin-arm64 ai-cli
xattr -dr com.apple.quarantine ./ai-cli
./ai-cli version
```

Intel: same steps with `aioz-ai-cli-darwin-amd64-0.24.tar.gz` / `aioz-ai-cli-darwin-amd64`. Do not use the Intel archive on Apple Silicon.

`version` (keys sorted):

```json
{
  "built": "2026-09-11T04:38:44Z",
  "commit": "v0.24.0-demo",
  "version": "0.24"
}
```

## Getting started

### Create a key

```bash
./ai-cli keytool new --save-priv-key privkey.json
```

Windows: `.\ai-cli.exe keytool new --save-priv-key privkey.json`.

`--save-priv-key` writes the private key JSON (mode `0600`). Store the mnemonic now; it is not shown again. This does not create a data folder.

```json
{
  "address": "aioz1…",
  "address_evm": "0xAbc0…def1",
  "mnemonic": "twelve words …",
  "priv_key_file": "privkey.json"
}
```

Treat `privkey.json` and the mnemonic as **wallet secrets**. Use a **dedicated key for each node**. Keep an offline backup. Never paste them into websites, chats, or support tickets.

### Set storage limit

Required **before** `start`. The value must be **greater than 2 GB**. There is no 2 GB default. Bare `storage` prints help; use `storage limit` / `storage show`.

```bash
./ai-cli storage limit 10 --priv-key-file privkey.json
```

```json
{
  "home": "~/.local/share/aioz/ai-nodes/<uuid>",
  "limit_bytes": 10000000000,
  "limit_gb": 10,
  "storage_dir": "~/.local/share/aioz/ai-nodes/<uuid>"
}
```

`limit_bytes` is decimal GB (`10` → `10000000000`). Raise the cap by running the same command again; it applies on the next `start`.

Without `--home`, this wallet gets a UUID folder under:

| OS | Home |
|----|------|
| Linux | `~/.local/share/aioz/ai-nodes/<uuid>/` |
| Windows | `%LOCALAPPDATA%\AIOZ\ai-cli\ai-nodes\<uuid>\` |
| macOS | `~/Library/Application Support/AIOZ/ai-cli/ai-nodes/<uuid>/` |

### Start

```bash
./ai-cli start --priv-key-file privkey.json
```

`--priv-key-file` is required. `start` **prints one JSON object, then stays in the foreground**. It does **not** stream logs to stdout. Runtime logs go to `log_path`. The process waits until you press **Ctrl+C** (stops **this wallet only**) or the node exits.

```json
{
  "data_dir": "~/.local/share/aioz/ai-nodes/<uuid>",
  "evm_address": "0xAbc0…def1",
  "home": "~/.local/share/aioz/ai-nodes/<uuid>",
  "log_path": "~/.local/state/aioz/ai-cli/logs/<uuid>/ai.log",
  "pid": 12345,
  "pid_path": "~/.local/state/aioz/ai-cli/<uuid>/node.pid",
  "running": true,
  "storage_bytes": 10000000000,
  "update": {
    "skipped": false,
    "newer": false,
    "current": "0.24",
    "current_commit": "v0.24.0-demo",
    "remote": "0.24",
    "remote_commit": "v0.24.0-demo",
    "note": "CLI is up to date"
  }
}
```

Ctrl+C then prints a **second** JSON object. `running` here is a **count** of other node processes still live (not a boolean):

```json
{
  "running": 0,
  "status": "stopped"
}
```

If this wallet is already running, the first object is only `running`, `pid`, `pid_path`, plus `update`.

Log paths:

| OS | `log_path` |
|----|------------|
| Linux | `~/.local/state/aioz/ai-cli/logs/<uuid>/ai.log` |
| Windows | `%LOCALAPPDATA%\AIOZ\ai-cli\logs\<uuid>\ai.log` |
| macOS | `~/Library/Logs/AIOZ/ai-cli/<uuid>/ai.log` |

## Usage

### Status

```bash
./ai-cli status --priv-key-file privkey.json
```

```json
{
  "evm_address": "0xAbc0…def1",
  "home": "~/.local/share/aioz/ai-nodes/<uuid>",
  "log_path": "~/.local/state/aioz/ai-cli/logs/<uuid>/ai.log",
  "other_running": 0,
  "running": false
}
```

`ai-cli status --all` lists every indexed home (`homes`, `nodes`, `running`). No `--priv-key-file`.

### Storage show

```bash
./ai-cli storage show --priv-key-file privkey.json
```

```json
{
  "error": null,
  "near_full": false,
  "storage_limit": "10000000000",
  "storage_used": "0"
}
```

`storage_limit` / `storage_used` are **byte strings**. `near_full` is true at ≥ 90% of the cap.

### Logs

`logs` is a **snapshot** (last `--bytes`, default 32 KiB), not a live follow. Secrets in the file are redacted.

```bash
./ai-cli logs --priv-key-file privkey.json
```

```json
{
  "home": "~/.local/share/aioz/ai-nodes/<uuid>",
  "log": "… redacted snapshot …",
  "path": "~/.local/state/aioz/ai-cli/logs/<uuid>/ai.log",
  "wallet": "0xAbc0…def1"
}
```

For a live UI, tail `path` / `log_path` on disk.

### Reward balance

Works with the node off.

```bash
./ai-cli reward balance --priv-key-file privkey.json
```

```json
{
  "earned": {
    "amount": "0",
    "denom": "attoaioz",
    "aioz": "0"
  },
  "earned_count": 0,
  "spendable": {
    "amount": "0",
    "denom": "attoaioz",
    "aioz": "0"
  }
}
```

### Withdraw

`--address` is a MetaMask `0x` on AIOZ Chain. `--amount` is in AIOZ. Minimum **0.01 AIOZ**. `--yes` skips the confirm prompt.

```bash
./ai-cli reward withdraw --address 0xAbc0…def1 --amount 1 --priv-key-file privkey.json --yes
```

```json
{
  "txid": "2604F553…944D59"
}
```

### Recover from mnemonic

Do not pass the mnemonic on the command line (shell history / process list). Put the words in a file:

```bash
./ai-cli keytool recover --mnemonic-file words.txt --save-priv-key privkey.json
```

```json
{
  "address": "aioz1…",
  "address_evm": "0xAbc0…def1",
  "priv_key_file": "privkey.json"
}
```

### Update

```bash
./ai-cli update
```

```json
{
  "skipped": false,
  "newer": false,
  "current": "0.24",
  "current_commit": "v0.24.0-demo",
  "remote": "0.24",
  "remote_commit": "v0.24.0-demo",
  "note": "CLI is up to date"
}
```

### Stats

Hub snapshot (`wallet_address` is the hub field name):

```bash
./ai-cli stats --priv-key-file privkey.json
```

```json
{
  "ai_tasks": [],
  "error": null,
  "status": "Online",
  "wallet_address": "0xAbc0…def1"
}
```

If the hub is unreachable, this command prints an error on **stderr** instead of JSON.

### Doctor

```bash
./ai-cli doctor --priv-key-file privkey.json
```

```json
{
  "ok": true,
  "checks": [
    {"name": "os", "ok": true, "detail": "linux/amd64"},
    {"name": "home", "ok": true, "detail": "~/.local/share/aioz/ai-nodes/<uuid>"},
    {"name": "disk", "ok": true, "detail": "100 GB free"},
    {"name": "runtime", "ok": true, "detail": "ok"},
    {"name": "wallet", "ok": true, "detail": "0xAbc0…def1"},
    {"name": "log", "ok": true, "detail": "~/.local/state/aioz/ai-cli/logs/<uuid>/ai.log"},
    {"name": "identity", "ok": true, "detail": "credential is --priv-key-file"},
    {"name": "gpu", "ok": true, "detail": "NVIDIA, 8 GB"}
  ]
}
```
