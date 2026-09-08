## AIOZ AI CLI

Linux amd64 and Windows amd64 demo of the AIOZ AI operator CLI. This GitHub repository is **download + version-check only**. It is not the source tree. Only the **latest** release is kept.

## What is AIOZ AI CLI?

AIOZ AI CLI (`ai-cli`) is a command-line application that runs an AIOZ AI node on your machine, takes AI tasks, and earns AIOZ rewards.

The node runtime and keytool are **bundled inside the binary** and extracted on first use.

## Requirements

- Windows 10 64-bit (amd64) or later
- Ubuntu 20.04 64-bit (amd64) or later

macOS is not published in this demo.

## Install

### Windows

Download and extract the latest AIOZ AI CLI. The scripts below are written for Windows PowerShell.

```powershell
curl.exe -LO https://github.com/DiseAgon/aioz-ai-cli-demo/releases/latest/download/aioz-ai-cli-windows-amd64-0.17.zip
Expand-Archive -Path aioz-ai-cli-windows-amd64-0.17.zip -DestinationPath .
ren aioz-ai-cli-windows-amd64.exe ai-cli.exe
```

Verify the installation:

```powershell
.\ai-cli.exe version
```

Use a normal PowerShell window. **Do not** "Run as administrator". Data is under `%LOCALAPPDATA%\AIOZ\ai-cli\` (your user profile). The first `start` may show a Windows Firewall prompt; allow it for private networks.

### Linux and macOS

macOS archives are not published yet. For Linux amd64:

```bash
curl -LO https://github.com/DiseAgon/aioz-ai-cli-demo/releases/latest/download/aioz-ai-cli-linux-amd64-0.17.tar.gz
tar -xzf aioz-ai-cli-linux-amd64-0.17.tar.gz
mv aioz-ai-cli-linux-amd64 ai-cli
```

Verify the installation:

```bash
./ai-cli version
```

When macOS builds are published, the archives will be named `aioz-ai-cli-darwin-arm64-<version>.tar.gz` and `aioz-ai-cli-darwin-x86_64-<version>.tar.gz`.

**Note:** On Windows PowerShell, type `.\ai-cli.exe` (the `.\` is required; `ai-cli.exe` alone is not found). On Linux and macOS use `./ai-cli`.

Response:

```
╭─ ai-cli ─────────────────────────────────────────────────────────────╮
│                                                                      │
│  Version           0.17                                              │
│  Commit            v0.17.0-demo                                      │
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

For Linux

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

For Linux

```bash
./ai-cli storage limit 10 --priv-key-file privkey.json
```

Response:

```
╭─ storage ────────────────────────────────────────────────────────────╮
│                                                                      │
│  Limit             10 GB                                             │
│  Home              ~/.local/share/aioz/ai-nodes/<uuid>/              │
│  Dir               ~/.local/share/aioz/ai-nodes/<uuid>/              │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

Run the same command again later to raise the cap. It is applied on the next `start`.

## Start ai-node

For Windows

```powershell
.\ai-cli.exe start --priv-key-file privkey.json
```

For Linux

```bash
./ai-cli start --priv-key-file privkey.json
```

`--priv-key-file` is required. Ctrl+C stops **this wallet only**.

Response:

```
╭─ node ───────────────────────────────────────────────────────────────╮
│                                                                      │
│  Status            running                                           │
│  CLI               0.17                                              │
│  Update            CLI is up to date                                 │
│  PID               12345                                             │
│  Home              ~/.local/share/aioz/ai-nodes/<uuid>/              │
│  Storage           10 GB                                             │
│  Dir               ~/.local/share/aioz/ai-nodes/<uuid>/              │
│  ETH               0xAbc0…def1                                       │
│                                                                      │
│  ⚠                 streaming logs; Ctrl+C to stop the node           │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
INFO  node running; Ctrl+C to stop
```

Without `--home`, data for this wallet is Linux `~/.local/share/aioz/ai-nodes/<uuid>/` or Windows `%LOCALAPPDATA%\AIOZ\ai-cli\ai-nodes\<uuid>\`. The wallet label is `identity/address` (next to the pid file), not the folder name.

## Usage

### Node status

For Windows

```powershell
.\ai-cli.exe status --priv-key-file privkey.json
```

For Linux

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

For Linux

```bash
./ai-cli status --all
```

Lists every home on this machine. Does not need `--priv-key-file`.

### Show storage

For Windows

```powershell
.\ai-cli.exe storage show --priv-key-file privkey.json
```

For Linux

```bash
./ai-cli storage show --priv-key-file privkey.json
```

Response:

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

For Linux

```bash
./ai-cli reward balance --priv-key-file privkey.json
```

Works with the node off.

Response:

```
╭─ reward ─────────────────────────────────────────────────────────────╮
│                                                                      │
│  Spendable         2.5 AIOZ                                          │
│  Earned            2.5 AIOZ                                          │
│  Count             10                                                │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

### Withdraw reward

For Windows

```powershell
.\ai-cli.exe reward withdraw --address 0xAbc0…def1 --amount 1 --priv-key-file privkey.json --yes
```

For Linux

```bash
./ai-cli reward withdraw --address 0xAbc0…def1 --amount 1 --priv-key-file privkey.json --yes
```

Response:

```
╭─ withdraw ───────────────────────────────────────────────────────────╮
│                                                                      │
│  Tx                2604F553…944D59                                   │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

**Note:** `--address` is a MetaMask `0x` on AIOZ Chain. `--amount` is in AIOZ. Minimum withdraw is **0.01 AIOZ**. `--yes` skips the confirm prompt.

### Recover private key from mnemonic phrase

The sidecar does not take the mnemonic as a command argument (it would leak in shell history and the process list). Put the words in a file, then:

For Windows

```powershell
.\ai-cli.exe keytool recover --mnemonic-file words.txt --save-priv-key privkey.json
```

For Linux

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

For Linux

```bash
./ai-cli update
```

Response:

```
CLI is up to date
```

### Stats / Logs

For Windows

```powershell
.\ai-cli.exe stats --priv-key-file privkey.json
```

For Linux

```bash
./ai-cli stats --priv-key-file privkey.json
```

Response:

```
╭─ node ───────────────────────────────────────────────────────────────╮
│                                                                      │
│  Status            Offline                                           │
│  Wallet            0xAbc0…def1                                       │
│                                                                      │
╰──────────────────────────────────────────────────────────────────────╯
```

For Windows

```powershell
.\ai-cli.exe logs --priv-key-file privkey.json
```

For Linux

```bash
./ai-cli logs --priv-key-file privkey.json
```

Tails this wallet's `ai.log` (redacted on screen).

### Doctor

For Windows

```powershell
.\ai-cli.exe doctor --priv-key-file privkey.json
```

For Linux

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
