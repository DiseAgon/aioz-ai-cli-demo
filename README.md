# ai-cli demo downloads

This repository holds **GitHub Release binaries** for the AIOZ AI operator CLI (version check + install).

- **Source code** stays on internal GitLab. Do not treat this repo as the source tree.
- Install: set `AI_CLI_MANIFEST_URL` to the latest `manifest.json` on Releases, then run `scripts/fetch-from-manifest.sh` from the GitLab tree (or the curl commands in that script).
- `aiozAiNode` is installed as `runtime`. `keytool` keeps the name `keytool`.
