## AIOZ AI CLI

Linux amd64, Windows amd64, macOS Apple Silicon (arm64), and macOS Intel (amd64) demo of the AIOZ AI operator CLI. This GitHub repository is **download + version-check only**. It is not the source tree. Only the **latest** release is kept.

## What is AIOZ AI CLI?

AIOZ AI CLI (`ai-cli`) is a command-line application that runs an AIOZ AI node on your machine, takes AI tasks, and earns AIOZ rewards.

The node runtime and keytool are **bundled inside the binary** and extracted on first use.

## Requirements

- Windows 10 64-bit (amd64) or later
- Ubuntu 20.04 64-bit (amd64) or later
- macOS 12+ 64-bit, Apple Silicon (arm64) or Intel (amd64)

## Install

### Windows

Download and extract the latest AIOZ AI CLI. The scripts below are written for Windows PowerShell.

Work in **your** profile folder (not another user's `C:\Users\...`). PowerShell as that user, not Administrator:

```powershell
cd $env:USERPROFILE
curl.exe -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-windows-amd64-0.24.zip
Expand-Archive -Path aioz-ai-cli-windows-amd64-0.24.zip -DestinationPath .
ren aioz-ai-cli-windows-amd64.exe ai-cli.exe
```

Verify the installation:

```powershell
.\ai-cli.exe version
```

`--save-priv-key privkey.json` writes into the current folder. `Access is denied` means that folder is not yours — `cd $env:USERPROFILE` and retry. Data is under `%LOCALAPPDATA%\AIOZ\ai-cli\`. The first `start` may show a Windows Firewall prompt; allow it for private networks.

### Linux

```bash
curl -fsSL https://github.com/DiseAgon/os-pack-dl/releases/latest/download/install.sh | bash
```

Or download the archive:

```bash
curl -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-linux-amd64-0.24.tar.gz
tar -xzf aioz-ai-cli-linux-amd64-0.24.tar.gz
mv aioz-ai-cli-linux-amd64 ai-cli
```

Verify the installation:

```bash
./ai-cli version
```

### macOS

`install.sh` picks Apple Silicon vs Intel from `uname -m`:

```bash
curl -fsSL https://github.com/DiseAgon/os-pack-dl/releases/latest/download/install.sh | bash
```

Or download the matching archive:

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

Intel:

```bash
curl -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-darwin-amd64-0.24.tar.gz
tar -xzf aioz-ai-cli-darwin-amd64-0.24.tar.gz
mv aioz-ai-cli-darwin-amd64 ai-cli
xattr -dr com.apple.quarantine ./ai-cli
./ai-cli version
```

Do not use the Intel archive on Apple Silicon. Data lives under `~/Library/Application Support/AIOZ/ai-cli/`, cache `~/Library/Caches/AIOZ/ai-cli`, logs `~/Library/Logs/AIOZ/ai-cli`.

**Note:** On Windows PowerShell, type `.\ai-cli.exe` (the `.\` is required; `ai-cli.exe` alone is not found). On Linux and macOS use `./ai-cli`.

Command stdout is indented JSON (no `--json` flag). Help stays human.

```json
{
  "version": "0.24",
  "commit": "v0.24.0-demo",
  "built": "2026-09-11T00:00:00Z"
}
```

## Getting started

### Create wallet_address, private key

For Windows

```powershell
.\ai-cli.exe keytool new --save-priv-key privkey.json
```

For Linux and macOS

```bash
./ai-cli keytool new --save-priv-key privkey.json
```

`--save-priv-key` writes the private key JSON (mode `0600`). Store the mnemonic now; it is not shown again. This does not create a data folder.

```json
{
  "address_evm": "0xAbc0…def1",
  "mnemonic": "… twelve words …",
  "priv_key_file": "privkey.json"
}
```

**IMPORTANT**

- Treat `privkey.json` and the mnemonic as **wallet secrets**. Anyone who has them can control this node's rewards.
- Use a **dedicated key for each node**. Do not reuse a main wallet, exchange key, or another node's key.
- Keep an offline backup. **Never paste** the mnemonic or private key into websites, chats, or support tickets.

## Set storage limit

This step is **required before `start`**. The value must be **greater than 2 GB**. There is no 2 GB default. `start` fails if the cap is missing.

For Windows

```powershell
.\ai-cli.exe storage limit 10 --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli storage limit 10 --priv-key-file privkey.json
```

```json
{
  "limit_gb": 10,
  "limit_bytes": 10000000000,
  "home": "~/.local/share/aioz/ai-nodes/<uuid>/",
  "storage_dir": "~/.local/share/aioz/ai-nodes/<uuid>/"
}
```

Run the same command again later to raise the cap. It is applied on the next `start`.

## Start ai-node

For Windows

```powershell
.\ai-cli.exe start --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli start --priv-key-file privkey.json
```

`--priv-key-file` is required. Ctrl+C stops **this wallet only**.

Stdout stays JSON (logs are not streamed). Ctrl+C still stops this wallet. Read logs with `ai-cli logs --priv-key-file privkey.json`.

```json
{
  "running": true,
  "pid": 12345,
  "home": "~/.local/share/aioz/ai-nodes/<uuid>/",
  "evm_address": "0xAbc0…def1",
  "storage_bytes": 10000000000
}
```

Without `--home`, data for this wallet is Linux `~/.local/share/aioz/ai-nodes/<uuid>/`, Windows `%LOCALAPPDATA%\AIOZ\ai-cli\ai-nodes\<uuid>/`, or macOS `~/Library/Application Support/AIOZ/ai-cli/home`. The wallet label is `identity/address` (next to the pid file), not the folder name.

## Usage

### Node status

For Windows

```powershell
.\ai-cli.exe status --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli status --priv-key-file privkey.json
```

```json
{
  "home": "~/.local/share/aioz/ai-nodes/<uuid>/",
  "running": false,
  "evm_address": "0xAbc0…def1"
}
```

For Windows

```powershell
.\ai-cli.exe status --all
```

For Linux and macOS

```bash
./ai-cli status --all
```

Lists every home on this machine. Does not need `--priv-key-file`.

### Show storage

For Windows

```powershell
.\ai-cli.exe storage show --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli storage show --priv-key-file privkey.json
```

```json
{
  "storage_limit": "10000000000",
  "storage_used": "1200000000",
  "near_full": false,
  "error": null
}
```

Warns when used is at least 90% of the cap.

### View reward

For Windows

```powershell
.\ai-cli.exe reward balance --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli reward balance --priv-key-file privkey.json
```

Works with the node off.

```json
{
  "spendable": {"amount": "2500000000000000000", "denom": "attoaioz", "aioz": "2.5"},
  "earned": {"amount": "2500000000000000000", "denom": "attoaioz", "aioz": "2.5"},
  "earned_count": 10
}
```

### Withdraw reward

For Windows

```powershell
.\ai-cli.exe reward withdraw --address 0xAbc0…def1 --amount 1 --priv-key-file privkey.json --yes
```

For Linux and macOS

```bash
./ai-cli reward withdraw --address 0xAbc0…def1 --amount 1 --priv-key-file privkey.json --yes
```

```json
{
  "txid": "2604F553…944D59"
}
```

**Note:** `--address` is a MetaMask `0x` on AIOZ Chain. `--amount` is in AIOZ. Minimum withdraw is **0.01 AIOZ**. `--yes` skips the confirm prompt.

### Recover private key from mnemonic phrase

The sidecar does not take the mnemonic as a command argument (it would leak in shell history and the process list). Put the words in a file, then:

For Windows

```powershell
.\ai-cli.exe keytool recover --mnemonic-file words.txt --save-priv-key privkey.json
```

For Linux and macOS

```bash
./ai-cli keytool recover --mnemonic-file words.txt --save-priv-key privkey.json
```

```json
{
  "address_evm": "0xAbc0…def1",
  "priv_key_file": "privkey.json"
}
```

### Update

For Windows

```powershell
.\ai-cli.exe update
```

For Linux and macOS

```bash
./ai-cli update
```

```json
{
  "skipped": false,
  "newer": false,
  "current": "0.24",
  "note": "CLI is up to date"
}
```

### Stats / Logs

For Windows

```powershell
.\ai-cli.exe stats --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli stats --priv-key-file privkey.json
```

```json
{
  "status": "Offline",
  "wallet_address": "0xAbc0…def1",
  "error": null
}
```

For Windows

```powershell
.\ai-cli.exe logs --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli logs --priv-key-file privkey.json
```

Tails this wallet's `ai.log` (redacted on screen).

### Doctor

For Windows

```powershell
.\ai-cli.exe doctor --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli doctor --priv-key-file privkey.json
```

```json
{
  "ok": true,
  "checks": [
    {"name": "os", "ok": true, "detail": "linux/amd64"}
  ]
}
```
