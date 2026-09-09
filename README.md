# Host CLI

Linux amd64, Windows amd64, and macOS (Intel + Apple Silicon) demo of **Host CLI** (`ai-cli`). This GitHub repository is **download + version-check only**. It is not the source tree. Only the **latest** release is kept.

## What is Host CLI?

Host CLI (`ai-cli`) is a command-line application that runs a node on your machine, takes tasks, and earns rewards.

The node runtime and keytool are **bundled inside the binary** and extracted on first use.

## Requirements

- Windows 10 64-bit (amd64) or later
- Ubuntu 20.04 64-bit (amd64) or later
- macOS 12+ 64-bit, Apple Silicon (arm64) or Intel (amd64)

## Install

### Windows

Download and extract the latest Host CLI. The scripts below are written for Windows PowerShell.

Work in **your** profile folder (not another user's `C:\Users\...`). PowerShell as that user, not Administrator:

```powershell
cd $env:USERPROFILE
curl.exe -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-windows-amd64-0.22.zip
Expand-Archive -Path aioz-ai-cli-windows-amd64-0.22.zip -DestinationPath .
ren aioz-ai-cli-windows-amd64.exe ai-cli.exe
```

Or run the installer:

```powershell
irm https://github.com/DiseAgon/os-pack-dl/releases/latest/download/install.ps1 | iex
```

Verify the installation:

```powershell
.\ai-cli.exe version
```

`--save-priv-key privkey.json` writes into the current folder. `Access is denied` means that folder is not yours — `cd $env:USERPROFILE` and retry. Data is under `%LOCALAPPDATA%\AIOZ\ai-cli\`. The first `start` may show a Windows Firewall prompt; allow it for private networks.

### Linux

```bash
curl -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-linux-amd64-0.22.tar.gz
tar -xzf aioz-ai-cli-linux-amd64-0.22.tar.gz
mv aioz-ai-cli-linux-amd64 ai-cli
```

Or:

```bash
curl -fsSL https://github.com/DiseAgon/os-pack-dl/releases/latest/download/install.sh | bash
source ~/.bashrc
```

Verify the installation:

```bash
./ai-cli version
```

### macOS

Two archives, one per chip. Pick with `uname -m`:

| `uname -m` | Chip | Archive | Inner file |
|------------|------|---------|------------|
| `arm64` | Apple Silicon (M1–M4) | `aioz-ai-cli-darwin-arm64-0.22.tar.gz` | `aioz-ai-cli-darwin-arm64` |
| `x86_64` | Intel | `aioz-ai-cli-darwin-amd64-0.22.tar.gz` | `aioz-ai-cli-darwin-amd64` |

Apple Silicon:

```bash
curl -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-darwin-arm64-0.22.tar.gz
tar -xzf aioz-ai-cli-darwin-arm64-0.22.tar.gz
mv aioz-ai-cli-darwin-arm64 ai-cli
xattr -dr com.apple.quarantine ./ai-cli
./ai-cli version
```

Intel:

```bash
curl -LO https://github.com/DiseAgon/os-pack-dl/releases/latest/download/aioz-ai-cli-darwin-amd64-0.22.tar.gz
tar -xzf aioz-ai-cli-darwin-amd64-0.22.tar.gz
mv aioz-ai-cli-darwin-amd64 ai-cli
xattr -dr com.apple.quarantine ./ai-cli
./ai-cli version
```

Or use `install.sh` (picks the archive for this machine). Do not use the Intel archive on Apple Silicon. Data lives under `~/Library/Application Support/AIOZ/ai-cli/`, cache `~/Library/Caches/AIOZ/ai-cli`, logs `~/Library/Logs/AIOZ/ai-cli`.

**Note:** On Windows PowerShell, type `.\ai-cli.exe` (the `.\` is required; `ai-cli.exe` alone is not found). On Linux and macOS use `./ai-cli`.

Response:

```
╭─ ai-cli ─────────────────────────────────────────────────────────────╮
│                                                                      │
│  Version           0.22                                              │
│  Commit            v0.22.0-demo                                      │
│  Built             2026-01-01T00:00:00Z                              │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
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

Response:

```
╭─ keytool ────────────────────────────────────────────────────────────╮
│                                                                      │
│  Node ETH          0xAbc0…def1                                       │
│  Mnemonic          … twelve words …                                  │
│                                                                      │
│  ⚠                 saved privkey.json                                │
│                    (store the mnemonic now; it is not shown again)   │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
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

Response:

```
╭─ storage ────────────────────────────────────────────────────────────╮
│                                                                      │
│  Limit             10 GB                                             │
│  Home              ~/.local/share/aioz/ai-nodes/<uuid>/              │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

Run the same command again later to raise the cap. It is applied on the next `start`.

## Start the node

For Windows

```powershell
.\ai-cli.exe start --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli start --priv-key-file privkey.json
```

`--priv-key-file` is required. Ctrl+C stops **this wallet only**. There is no `stop` command.

Response:

```
╭─ node ───────────────────────────────────────────────────────────────╮
│                                                                      │
│  Status            running                                           │
│  CLI               0.22                                              │
│  PID               12345                                             │
│  Home              ~/.local/share/aioz/ai-nodes/<uuid>/              │
│  Storage           10 GB                                             │
│  ETH               0xAbc0…def1                                       │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

Without `--home`, data for this wallet is Linux `~/.local/share/aioz/ai-nodes/<uuid>/`, Windows `%LOCALAPPDATA%\AIOZ\ai-cli\ai-nodes\<uuid>/`, or macOS `~/Library/Application Support/AIOZ/ai-cli/`. The wallet label is `identity/address` (next to the pid file), not the folder name.

## Usage

### Node status

Shows whether this wallet's node is running, plus its home and ETH address.

For Windows

```powershell
.\ai-cli.exe status --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli status --priv-key-file privkey.json
```

Response:

```
╭─ node ───────────────────────────────────────────────────────────────╮
│                                                                      │
│  Home              ~/.local/share/aioz/ai-nodes/<uuid>/              │
│  Status            stopped                                           │
│  ETH               0xAbc0…def1                                       │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
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

Shows the storage cap and how much is used. Used is **0** until the node has started.

For Windows

```powershell
.\ai-cli.exe storage show --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli storage show --priv-key-file privkey.json
```

Response (before `start`):

```
╭─ storage ────────────────────────────────────────────────────────────╮
│                                                                      │
│  Limit             10 GB                                             │
│  Used              0 B                                               │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

Response (after `start`):

```
╭─ storage ────────────────────────────────────────────────────────────╮
│                                                                      │
│  Limit             10 GB                                             │
│  Used              1.2 GB                                            │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
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

Response:

```
╭─ reward ─────────────────────────────────────────────────────────────╮
│                                                                      │
│  Spendable         2.5 HOST                                          │
│  Earned            2.5 HOST                                          │
│  Count             10                                                │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

### Withdraw reward

For Windows

```powershell
.\ai-cli.exe reward withdraw --address 0xAbc0…def1 --amount 0.01 --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli reward withdraw --address 0xAbc0…def1 --amount 0.01 --priv-key-file privkey.json
```

Without `--yes` the CLI asks:

```
Withdraw 0.01 HOST (10000000000000000 attohost) to MetaMask 0xAbc0…def1? [y/N]
```

Response:

```
╭─ withdraw ───────────────────────────────────────────────────────────╮
│                                                                      │
│  Tx                6c3fb8ab-fda6-408c-8dbe-34f3190ce837              │
│  To MetaMask       0xAbc0…def1                                       │
│  Amount            0.01 HOST                                         │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

**Note:** `--address` is a MetaMask `0x`. `--amount` is in HOST. Minimum withdraw is **0.01 HOST**. `--yes` skips the confirm prompt.

### Recover private key from mnemonic phrase

The CLI does not take the mnemonic as a command argument (it would leak in shell history and the process list). Put the words in a file, then:

For Windows

```powershell
.\ai-cli.exe keytool recover --mnemonic-file words.txt --save-priv-key privkey.json
```

For Linux and macOS

```bash
./ai-cli keytool recover --mnemonic-file words.txt --save-priv-key privkey.json
```

Response:

```
╭─ keytool ────────────────────────────────────────────────────────────╮
│                                                                      │
│  Node ETH          0xAbc0…def1                                       │
│                                                                      │
│  ⚠                 saved privkey.json                                │
│                    (store the mnemonic now; it is not shown again)   │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
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

Response:

```
CLI is up to date
```

Builds before **0.19** cannot self-update (they look for a bare `ai-cli` asset). Install **0.19 or later** once, then `update` works.

### Stats / Logs

For Windows

```powershell
.\ai-cli.exe stats --priv-key-file privkey.json
```

For Linux and macOS

```bash
./ai-cli stats --priv-key-file privkey.json
```

**Status** is the string the node returns (for example `Coming soon` or `Standby`).

Response:

```
╭─ node ───────────────────────────────────────────────────────────────╮
│                                                                      │
│  Status            Coming soon                                       │
│  Wallet            0xAbc0…def1                                       │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
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

Response:

```
╭─ doctor ─────────────────────────────────────────────────────────────╮
│                                                                      │
│  Overall           ok                                                │
│  Os                linux/amd64                                       │
│  Home              ~/.local/share/aioz/ai-nodes/<uuid>/              │
│  Disk              100 GB free                                       │
│  Runtime           ok                                                │
│  Wallet            0xAbc0…def1                                       │
│  Log               ~/.local/state/aioz/ai-cli/logs/<uuid>/ai.log     │
│  Identity          credential is --priv-key-file                     │
│  Gpu               NVIDIA, 8 GB                                      │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```
