---
name: esggo-oa-team-swarm
description: >
  End-to-end ESG-GO OA-Team 30 swarm deployment and soul-aligned bootstrap workflow.
  Use when standing up, recovering, or hardening the esggo VPS/bootstrap/swarm stack:
  VPS IP/recovery, Docker rebuild, compose/service validation, 30-agent swarm launch,
  soul/best-practice embedding, agent role registration, CI/fixup sweeps,
  cleanup of dumped-env/residual workflows, and goal-first handoff without confirming each step.
  Trigger keywords: OA-Team 30, swarm start --agents=30, /opt/esggo, esggo-core, omniagent-gateway, esggo_vps,
  soul.md, best-practice awakening, ESGGOCI fixes, dump-env, VPS recovery.
version: 1.0.0
author: Hermes Agent / DingJun1028
license: MIT
metadata:
  hermes:
    tags: [esggo, swarm, vps, docker, agents-cli, soul, best-practice, deployment]
    related_skills: [esggo-vps-toolkit, hermes-usage-best-practices, agent-role-registry, omni-auto, oa-team-soul-canon]
---

# ESG-GO OA-Team 30 Swarm Deployment

## Overview

This is the **class-level workflow** for turning an esggo VPS into a running OA-Team 30 swarm
with embedded soul/best-practice contracts. It emphasizes durable artifacts over ad-hoc instructions,
and outcome-first over step-by-step confirmation.

## When to Use

- User asks to bootstrap, recover, or redeploy `esggo` on the VPS.
- Task involves `agents-cli swarm start --agents=30` or validating swarm readiness.
- Session output mentions `C:\Project\esggo`, `/opt/esggo`, docker-compose health issues,
  missing `DATABASE_URL`, built-in `curl`, nginx proxy/DNS updates, or VPS IP change.
- The user pastes new secrets/keys and expects you to route them to GitHub Secrets/Firebase/GCP,
  not leave them exposed.
- The user asks for a VPS agent registry or role catalog tied to the swarm.
- The user requests soul/best-practice framework codification into `soul.md` or agent docs.

## 1. Preferred starting shape

Deliver a **deployment packet** in this order:
1. Recovery/access summary
2. Container/service readiness
3. Bootstrap artifacts
4. Launch/verification
5. Cleanup queue
6. Handoff if blocked

Do not narrate each step; do pass the user the exact blocker if an operation cannot be executed
from this environment.

## 2. VPS recovery and access

Stable defaults for this project:
- Primary key: `~/.ssh/esggo_original`
- Alternate aliases: `esggo-vps`, `esggo-vps-root`, `esggo-fix`
- Current VPS IP: `161.118.248.180`
- Deploy path: `/opt/esggo`
- Canonical source: OCI console public IP for instance `esggo-vps` overrides any prior memory of `161.118.252.147`.

SSH typo pitfall: if a connection attempt shows `Could not resolve hostname ...ssh`, the user likely concatenated `ssh` to the IP. Verify the host field is exactly `ubuntu@<IP>` with a space.

If `ssh` cannot be executed from the agent environment, explicitly state the single command the user
should run and the exact output shape you need next. Avoid repeating generic step lists.

### SSH unlock pitfall (2026-08-03 verified): `terminal.ssh_key` is a REQUIRED 6th key
`scripts/unlock-ssh.py` (in `hermes-memory-tencentdb-windows` skill) writes only 5 config keys
(backend / ssh_host / ssh_user / ssh_port / cwd) and **omits `terminal.ssh_key`**. After unlock +
restart, the SSH backend then authenticates via ssh-agent (no key loaded for `~/.ssh/esggo_original`),
so connections still fail. Diagnostic signature: the error message CHANGES from
`SSH environment requires ssh_host and ssh_user` → `SSH connection failed: getsockname failed` once
config is loaded — that means config took effect and the remaining problem is authentication, not
network. Fix (user runs in local PowerShell, then restarts Hermes so config reloads at process start):
```powershell
hermes config set terminal.ssh_key "$HOME\.ssh\esggo_original"
```
Verify: `Get-Content "$env:LOCALAPPDATA\hermes\config.yaml" | Select-String "ssh_key"`.
Also: `hermes-memory-tencentdb-windows` is user-owned (created_by=None) — do not try to patch it;
recommend `hermes curator adopt` if edits are needed there.

### SSH key corruption pitfall (2026-08-04 verified)
`esggo_original` is the LIVE key trusted by VPS `161.118.248.180`. NEVER run
`ssh-keygen -t rsa -b 4096 -f ~/.ssh/esggo_original` to "regenerate" it while in use:
if `~/.ssh` is missing the first attempt fails `No such file or directory`, and a later retry with the
same path can ZERO/overwrite the private key, breaking VPS trust (lock-out risk). Safe recovery when
`esggo_original` is damaged but a `.bak` exists:
1. Restore private key from backup (do NOT regenerate):
   ```powershell
   Copy-Item -Force ~/.ssh/esggo_original.bak.<timestamp> ~/.ssh/esggo_original
   ```
2. Verify VPS trust restored BEFORE touching anything else:
   ```powershell
   ssh -i ~/.ssh/esggo_original -o IdentitiesOnly=yes -o BatchMode=yes esggo-vps "echo ORIGINAL_RESTORED_OK"
   ```
   (prints ORIGINAL_RESTORED_OK => safe)
3. Restore the public key from the private key. Use `$HOME` absolute path — `ssh-keygen -y -f ~/.ssh/esggo_original`
   can fail `No such file or directory` under PowerShell `~` resolution quirks even when the file exists:
   ```powershell
   $key = "$HOME\.ssh\esggo_original"
   ssh-keygen -y -f $key > "$HOME\.ssh\esggo_original.pub"
   ```
Rule of thumb: if `ssh -i <key> esggo-vps` works, the key is valid — restore from `.bak`, never regenerate.
Note: `hermes config set terminal.ssh_key ...` prints "'terminal.ssh_key' is not a recognized config key,
saved anyway" — this warning is benign; the key is persisted and read after a full Hermes restart.
The definitive test is the agent attempting a real SSH connection post-restart, not the warning text.

### SSH backend self-lock pitfall (2026-08-07 verified — CRITICAL)
Setting `terminal.backend=ssh` in a LIVE session locks the agent's own `terminal` tool.
- Every `terminal` call — INCLUDING the recovery command `hermes config set terminal.backend local` — fails with
  `SSH connection failed: getsockname failed: Not a socket` because the SSH backend activates the instant config
  is written, but cannot read the 6 ssh keys until a FULL Hermes process restart.
- Error signature changing `SSH environment requires ssh_host and ssh_user` → `getsockname failed` proves config WAS
  loaded (backend=ssh took effect); remaining failure is pre-restart key unreadability, NOT a network issue.
- The agent CANNOT self-recover in-session: the `terminal` tool now routes through the broken SSH backend first.
FIX (agent must hand to user — single command):
- User runs in local PowerShell: `hermes config set terminal.backend local` → unlocks agent terminal immediately.
- Recommended permanent pattern: keep `terminal.backend=local` and drive the VPS via `ssh esggo-vps "..."`.
  Local-machine ssh works perfectly; ONLY Hermes's built-in SSH backend is broken on Windows (getsockname).
- The 6 ssh keys (backend/ssh_host/ssh_user/ssh_port/ssh_key/cwd) stay in config.yaml and are read after the user
  restarts Hermes — that is the ONLY correct verification moment. Do NOT claim SSH backend works until a
  post-restart real connection succeeds.

## 3. Container/service readiness pattern

Use this checklist after Docker changes:
- `docker ps`
- `docker compose -f vps/docker-compose.yml ps`
- health endpoint probe: `docker exec <svc> which curl && curl -I http://localhost:<port>`
- nginx status + domain routing validation if Cloudflare is involved

Rebuild rule: when new runtime tools like `curl` are missing and were installed in-container once,
rebuild the image so the change survives restarts rather than keeping it ad-hoc.

## 4. Environment and secrets hygiene

These are non-negotiable for this project:
- `DATABASE_URL` must be the Supabase **session pooler IPv4** form.
- Never commit `.env`/`.env.local`; use GitHub Secrets + VPS `/opt/esggo/vps/.env`.
- Rotating keys is a state transition: update GitHub Secrets first, then runtime/.env, then verify.
- Exposed credentials in chat must be treated as rotated/replaced; prompt rotation after use.

## 5. 30-agent swarm launch pattern — CORRECTED (2026-08-07)

⚠ PHANTOM COMMAND — `agents-cli swarm start --agents=30` does NOT exist in any verifiable source.
Verified by real SSH to VPS + `git ls-remote` (2026-08-07):
- `DingJun1028/esggo@v2.1.0` → package `esggo-python`, NO CLI entrypoint, NO `swarm` subcommand.
- `DingJun1028/agents-cli` → a clone of Google's `google-agents-cli` (identical README); root has NO `package.json`,
  so `npm install git+https://github.com/DingJun1028/agents-cli.git` FAILS with
  `ENOENT: no such file or directory, open '.../package.json'`. Google's `agents-cli` exposes only
  `setup/scaffold/eval/deploy/publish` — NO `swarm`.
- Google `google-agents-cli` (PyPI) → `uvx google-agents-cli setup`; no swarm command.
The prior `npm install ...DingJun1028/agents-cli.git` instruction is WRONG (no package.json) — DO NOT reuse it.
Never invent an `agents-cli` source for swarm launch.

REAL launch options for the 30-agent swarm (map to the 5 soul.md arrays, 6 agents each):
- (A) Hermes-native (preferred): `delegate_task` spawns up to 10 parallel subagents per batch; extend across batches
  to reach 30. This is the real, supported swarm primitive — no external CLI needed.
- (B) VPS-resident orchestrator: write a real `swarm-orchestrator` script (Python/Node) + systemd unit that keeps 30
  worker processes alive. No prebuilt tool exists for this; do not assume one does.
- (C) ONLY if the user supplies a GENUINE `agents-cli` source (private repo / local binary) — otherwise do NOT fabricate.

If blocked, provide the **single** command to run and the **single-shape** output to return:
either `成功`, or `失敗 + 錯誤訊息`. Do not request screenshots or repeated terminal dumps.

## 6. VPS agent registry shape

Prefer this frontmatter/schema for any VPS-related agent definition:
```yaml
name: vps-agent
description: VPS 部署代理 - 負責 ESGGO 平台所有伺服器相關事務
type: local
model: '...'
```

Service mapping stays explicit:
- Express API path on its defined port
- Next UI path on its defined port
- Gateway path on its defined port

Responsibilities map to one of: `servers`, `deploy`, `resources`.
Last updated + owner should always be present.

## 7. Soul/best-practice embedding

Treat `C:\Project\esggo\soul.md` as a **contract**, not a document.
Best-practice additions should include:
- definition
- the "無作妙德 圓通無礙 永恆覺醒" property, stated as a self-sustaining state
- the **結界** propagation rule: all agents, subagents, and swarm inherit it
- three hard rules: default-compliant, no known-issue launch, top-tier when awake
- ESG-GO alignment table: 5T, 4可1不可, Hash Lock, entropy < 0.1, 30 agents
- application table mapping it to VPS/image/CI/secrets/swarm workflows

## 8. Cleanup after deployment

Run cleanup in this priority order unless the user asks for stop:
1. Remove temporary dump workflows/branches: `dump-env.yml`, `temp-dump-env`
2. Remove validation-only workflow residue: `reset-db-pw.yml`, `fix-db` workflow remains if deprecated
3. Confirm `.gitignore` includes `.env` and `.env.local`
4. Update all scripts/docs from old IP to `161.118.248.180`

## 8b. Cron hygiene for esggo jobs

When reviewing or normalizing ESG-GO cron jobs:
- Delete duplicate jobs with identical intent.
- Update stale IPs/hostnames in prompts to the canonical VPS IP.
- Add OmniTag to every active job prompt:
  - VPS health: `[agent:13][squad:光之羽翼][lifecycle:active][p1][platform:vps][best-practice:awakened]`
  - Docker health: `[agent:14][squad:光之羽翼][lifecycle:active][p2][platform:vps][best-practice:结界]`
  - Daily report: `[agent:20][squad:報告投遞][lifecycle:active][p3][platform:omni][best-practice:结界]`
- Prefer `[SILENT]` unchanged output to reduce noise; only emit actionable deltas.
- Keep delivery channel aligned with job criticality: monitoring jobs use `all`; one-shot scripts use `local`.

### Model drift recovery pattern (2026-08-07 verified)
If cron jobs fail with `HTTP 404: Model '<model>' not found` or `Skipped to prevent unintended spend: global inference config drifted`:
1. Do NOT retry `cronjob action=update` with `model`/`provider` — it returns `No updates provided.`
2. Inspect `C:\Users\<user>\AppData\Local\hermes\cron\jobs.json` for the exact `last_error`.
3. Remove the broken jobs: `cronjob action=remove job_id=<id>`
4. Recreate them with the same name/schedule/prompt/deliver, but **leave `model` and `provider` empty** so they inherit the current session's working provider.
5. This preserves scheduling and intent while clearing the stale `model_snapshot`.

### Cron notification / "tracking letter" channel architecture (2026-08-08 verified)

Symptom: user reports "萬能分身追蹤信件從沒啟動過". Root cause is NOT a dead detector — it is a
broken DELIVERY chain. Diagnosis pattern (reuse for any "cron ran but I got nothing" report):

1. **Detector is fine**: `C:\Users\dingj\AppData\Local\hermes\scripts\gh-error-watch.py` polls
   `gh run list` and prints JSON `{new_failures:[...], action:'delegate'|'none'}`. Run it manually to confirm.
2. **Three breakpoints that kill delivery**:
   - (a) **Missing bridge cron**: the `telegram-vps-bridge` cron was deleted during a prior cron
     cleanup and never rebuilt → no notification channel exists. Verify with `cronjob action=list`;
     if absent, recreate it (see template below).
   - (b) **`deliver=local` trap**: a cron with `deliver: local` keeps its output LOCAL — it never
     reaches Telegram/chat/email. For user-visible notifications set `deliver: all` (or a
     `telegram:chat_id:thread_id` target). `deliver=local` is correct ONLY for silent one-shot scripts.
   - (c) **GitHub Actions secrets are UNREADABLE from local cron**: `gh secret` supports only
     list/set/delete — there is NO read-value. So `TELEGRAM_BOT_TOKEN`/`TELEGRAM_CHAT_ID` stored as
     repo secrets cannot be consumed by a local Hermes cron. A bridge cron that tries to curl
     Telegram with those values will silently fail to send. Source such tokens from a LOCAL file/env
     instead (e.g. `~/.hermes/.env`), never from repo secrets, for local-cron delivery.
3. **Working delivery channel = GitHub Issue**: `gh issue create --repo DingJun1028/esggo` works from
   local cron because `gh` CLI auth is local (keyring), independent of GitHub Actions secrets. This is
   the reliable "tracking letter" substitute — the user gets a GitHub notification per CI failure.
   Verified: issue #439 (`🐝 OA-TWINS 追蹤: Deploy to Oracle VPS #...`) was auto-created, proving the
   mechanism runs; the "letter" the user expected was email/Telegram, which the design never wired up.

Rebuild template for the bridge cron (GitHub Issue channel, no external-secret dependency):
```
cronjob action=create name=telegram-vps-bridge schedule="every 15m" deliver=all \
  skills='["esggo-ci-auto-repair"]' \
  prompt="你是 OA-TWINS 自動修復跟蹤員。每15分鐘: 1) 執行 python3 C:/Users/dingj/AppData/Local/hermes/scripts/gh-error-watch.py
  2) 若 action=='none' 靜默結束 3) 否則對每個 failure 用 gh issue create --repo DingJun1028/esggo
  開追蹤 issue (檢查重複 run_id 避免重開) 4) 繁體中文回報處理數。標題用 🐝 OA-TWINS 追蹤: <wf> #<run_id>。"
```
Pitfall: never set the bridge's prompt to "read TELEGRAM_* from gh secret" — it cannot. If real
Telegram-to-phone is required, have the user supply the token to a LOCAL file first.

## 8c. Push/deploy gotchas for this repo (verified 2026-08-04)

When the agent terminal SSH backend is unavailable, you CANNOT run `git push` / `git merge`.
Use the `my_server` MCP tools to verify repo state (read `.git/config`, `HEAD`, `packed-refs`,
`logs/HEAD`), then hand the user a local PowerShell/git sequence and ask them to paste output back.
Do NOT claim a deploy succeeded without real output (最佳實踐覺 覺之三).

- Agent SSH `getsockname failed: Not a socket` = local socket/auth issue, NOT VPS downtime.
  State the agent-side blocker honestly; do not blame the VPS.
- `esggo-learning-center/.gitignore` contains a `soul.md` line (top-level) → that file is ignored
  and will NOT be committed/pushed. Force-add: `git add -f soul.md soul-seed.md` before commit.
- Two parallel clones exist: `C:\Project\esggo-learning-center` (docs sandbox, holds soul.md /
  oa-components-definition.md) and `C:\Project\esggo` (main app, no soul.md). Both remote =
  `DingJun1028/esggo.git`. Confirm cwd before staging — pushing from the wrong clone misses the
  edited file.
- NOTE (fix §7): the live edit target is `C:\Project\esggo-learning-center\soul.md`, NOT
  `C:\Project\esggo\soul.md` as §7 text states — the latter path has no soul.md. Update §7 if
  you touch this skill again.
- Local sequence to give the user (run in PowerShell/Git Bash at the correct cwd):
  cd C:\Project\esggo-learning-center
  git add -f soul.md soul-seed.md
  git commit -m "docs(soul): vX.Y.Z 最佳實踐覺結界對齊"
  git checkout main
  git merge --no-ff <branch> -m "merge: <branch> -> main"   # repeat per branch to merge
  git push origin main
- VPS canonical IP = `161.118.248.180`; treat any `252.147` reference as stale (also see §2).

## 8c. Push/deploy gotchas for this repo (verified 2026-08-04)

When the agent terminal SSH backend is unavailable, you CANNOT run `git push` / `git merge`.
Use the `my_server` MCP tools to verify repo state (read `.git/config`, `HEAD`, `packed-refs`,
`logs/HEAD`), then hand the user a local PowerShell/git sequence and ask them to paste output back.
Do NOT claim a deploy succeeded without real output (最佳實踐覺 覺之三).

- Agent SSH `getsockname failed: Not a socket` = local socket/auth issue, NOT VPS downtime.
  State the agent-side blocker honestly; do not blame the VPS.
- `esggo-learning-center/.gitignore` contains a `soul.md` line (top-level) → that file is ignored
  and will NOT be committed/pushed. Force-add: `git add -f soul.md soul-seed.md` before commit.
- Two parallel clones exist: `C:\Project\esggo-learning-center` (docs sandbox, holds soul.md /
  oa-components-definition.md) and `C:\Project\esggo` (main app, no soul.md). Both remote =
  `DingJun1028/esggo.git`. Confirm cwd before staging — pushing from the wrong clone misses the
  edited file.
- NOTE (fix §7): the live edit target is `C:\Project\esggo-learning-center\soul.md`, NOT
  `C:\Project\esggo\soul.md` as §7 text states — the latter path has no soul.md. Update §7 if
  you touch this skill again.
- Local sequence to give the user (run in PowerShell/Git Bash at the correct cwd):
  cd C:\Project\esggo-learning-center
  git add -f soul.md soul-seed.md
  git commit -m "docs(soul): vX.Y.Z 最佳實踐覺結界對齊"
  git checkout main
  git merge --no-ff <branch> -m "merge: <branch> -> main"   # repeat per branch to merge
  git push origin main
- VPS canonical IP = `161.118.248.180`; treat any `252.147` reference as stale (also see §2).

### Hermes update & doctor on Windows (added 2026-08-06)

**Update pattern:**
1. Close all Hermes processes first (desktop app, terminals, gateway) — `taskkill /F /IM hermes.exe /T` and `taskkill /F /IM python.exe /T`. The venv process guard blocks updates otherwise.
2. Run `hermes update --yes --force` (skip `--force-venv` unless processes cannot be killed).
3. Verify with `hermes version` (not `hermes --version` which is a global flag). The correct check is `hermes version` which prints install metadata + "Up to date".

**Doctor timeout on v0.20.0+:**
`hermes doctor` hangs at the OpenRouter connectivity probe when no API key is configured. This is expected — the probe issues an unauthenticated HTTP GET to `https://openrouter.ai/api/v1/models` which can take >60s to timeout. Workaround: run `hermes doctor` with a 15s timeout or skip it; the real diagnostic is `hermes version` + `hermes auth list`.

**npm vulnerabilities in web workspace:**
`hermes doctor --fix` may not resolve all advisories (e.g., undici high-severity). Run `npm audit fix --force` in the web workspace, or bump the lockfile. These are build-time tooling advisories, not runtime issues.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

**Dashboard OAuth registration (v0.20.0+):**
To register a self-hosted dashboard with Nous Portal:
```bash
hermes dashboard register --name <label> [--redirect-uri <URI>]
```
- Omit `--redirect-uri` for localhost-only use (no auth gate on loopback bind).
- Provide `--redirect-uri https://yourdomain.com/auth/callback` for non-loopback binds.
- After registration, restart the dashboard to pick up the new env var.
- Manage or revoke dashboards at https://portal.nousresearch.com/local-dashboards

**Pitfall**: `hermes dashboard register` rejects `localhost` and `127.0.0.1` as redirect URIs — the portal auto-allows loopback, so omit the flag entirely for local use. Do not pass `--redirect-uri http://127.0.0.1:PORT/auth/callback`; it will fail with HTTP 400.

**Pitfall**: `hermes dashboard --skip-build` still runs a build step if dist is stale or missing. Pre-build with `cd web && npm run build` before using `--skip-build` in CI/background contexts. The `--skip-build` flag only skips the build if the dist directory already has valid artifacts.

For `OmniCore CI` style failures:
1. Inspect the real Vitest failure block first
2. Then address high-signal ESLint warnings in:
   - `src/impl/core.ts`
   - `src/agents/twelve-omni/omni-bus.ts`
   - `src/agents/twelve-omni/omni-api.ts`
3. Node 20 deprecation noise is not the blocker; only address if confirmed failing

## 10. User-preference rules for this class

- Traditional Chinese only; no Simplified Chinese.
- Autonomous execution is the default; confirm only on true blockers or secrets.
- Secrets are delivered as values to be stored in GitHub Secrets/GCP Secret Manager,
  not left exposed or repeated.
- If the agent cannot directly execute SSH/shell from the current environment,
  say so once and give the single command/output-shape needed next.

## 11. Computer Use fallback when terminal is stuck

When the SSH terminal is unresponsive (stuck in a reasoning loop, not accepting commands):
1. Use `computer_use` with `action='capture'` to take a screenshot of the terminal window
2. Use `computer_use` with `action='click'` on the terminal input element to focus it
3. Use `computer_use` with `action='type'` to send Ctrl+C and break out of the loop
4. Use `computer_use` with `action='type'` to send a fresh command
5. If the terminal window is not found via `app='WindowsTerminal'` or `app='PowerShell'`, try `app='hermes-agent'` or list all apps with `list_apps`

Pitfall: `computer_use` click coordinates change between sessions — always use `capture` first to get element indices, then click by `element=N` not by pixel coordinates.

Pitfall: The terminal may show the agent's reasoning output loop (repeated `preparing todo... plan 1/1 task(s) 0`) instead of accepting input. This is a terminal stuck-in-loop state. Break out with Ctrl+C first, then send a fresh command.

Pitfall (2026-08-04 verified, git/dir ops when SSH backend is down): `computer_use` typing is NOT a reliable substitute for terminal input on the local Windows Terminal. For `CASCADIA_HOSTING_WINDOW_CLASS`, `type` with `delivery_mode:'foreground'` is REJECTED ("not at UIAccess integrity, SetForegroundWindow subject to foreground-lock") because the cua-driver lacks UIAccess, and background `type` is silently dropped ("Background delivery is not available for this surface"). `focus_app(raise_window:true)` also fails ("No on-screen window found"). Result: looping on computer_use keystrokes wastes ~15 turns with no command ever landing. Reliable path for local `C:\Project\esggo` git when SSH is down: write a `.ps1` + wrapper `.bat` via MCP `my_server`, then ask the user to double-click it from a File Explorer THEY open (Win+E / double-click is also unreliable from computer_use), OR hand the user the exact one-shot PowerShell command. Critical: `git branch -r` over a large branch list triggers the `less` pager and swallows all follow-up keystrokes — always prefix with `git --no-pager` or set `$env:PAGER='cat'`.

If the terminal cannot be recovered, dispatch a background subagent via `delegate_task` to create files directly on the VPS, then check progress via the live transcript at `C:\Users\dingj\AppData\Local\hermes\cache\delegation\live\<delegation_id>\task-0.log`.

When the repo is outside the `my_server` open directory and no terminal is available,
use the **Explorer double-click** workflow:
1. Write a PowerShell script with **proper PowerShell syntax** — NOT Bash-style `if (cmd; $LASTEXITCODE)`.
2. Wrap it in a `.bat` that calls `powershell -NoProfile -ExecutionPolicy Bypass`.
3. Double-click the `.bat` in Explorer to execute.
4. Read the log to verify.

Pitfall: the first attempt used Bash-style `if (git diff --cached --quiet; $LASTEXITCODE -eq 1)`
which caused a PowerShell `ParserError` — the script never executed.
Always use PowerShell-native `if/else` with `$LASTEXITCODE` and curly braces `{ }`.

See `references/git-push-via-explorer.md` for the full pattern and verified script template.

## References

See `references/canonical-vps-and-agents-cli.md` for the canonical VPS identity AND the verified
2026-08-07 proof that `agents-cli swarm start --agents=30` is a PHANTOM command (no `agents-cli`
source ships a `swarm` subcommand — use `delegate_task` instead).

See `references/swarm-checks.md` for the success/failure probes to run after launching the 30-agent swarm
via `delegate_task` (the real, supported primitive — NOT the phantom `agents-cli swarm start --agents=30`).

See `references/omni-blueprint-hub-runbook.md` for the Omni-Blueprint Hub (萬能藍圖中心) build/run/verify pattern: native Node TS engine + RWD UI + monitor-server SSE broadcast, plus the Windows-terminal persistent-server + SSE verification technique.

See `references/soul-checklist.md` for the soul/best-practice addition checklist
and forbidden rewording rules.

See `references/soul-seed-workflow.md` for the soul.md chapter-推进 + seed-memory workflow:
逐章 MVP 壓縮交付、`C:\Project\esggo` 無寫權限（以貼入文本交付）、種子記憶雙重承載
(MCP 沙箱落檔 `soul-seed.md` + 本機 memory)、Hindsight 可能 402 需忠實回報、
及 memory 滿時用 batch remove+add 一次 transaction 控限。

See `references/queue-size-check-missing-secrets.md` for Cloudflare Worker queue
size check methodology when secrets are missing (relevant for webhook/queue monitoring).

See `references/dependabot-override-pattern.md` for the full Dependabot override resolution pattern with alert table, verification results, and key discoveries.

See `references/dependabot-override-pattern.md` for the full Dependabot override resolution pattern with alert table, verification results, and key discoveries.

See `references/dependabot-override-pattern.md` for the full Dependabot override resolution pattern with alert table, verification results, and key discoveries.

See `references/dependabot-override-pattern.md` for the full Dependabot override resolution pattern with alert table, verification results, and key discoveries.

See `references/dependabot-override-pattern.md` for the full Dependabot override resolution pattern with alert table, verification results, and key discoveries.

See `references/dependabot-override-pattern.md` for the full Dependabot override resolution pattern with alert table, verification results, and key discoveries.

See `references/dependabot-override-pattern.md` for the full Dependabot override resolution pattern with alert table, verification results, and key discoveries.

See `references/agent-reach-integration.md` for Agent-Reach perception layer integration: install modes (uvx local / systemd timer / Docker Compose), resilience circuit-breakers, data quality grading (L1/L2/L3), deployment topology, and Soul.md §7.1–7.5 enforcement rules.

See `references/cloudflare-worker-vision-setup.md` for Cloudflare Worker + Qwen3-VL vision model setup for deer-flow, including architecture, file layout, deployment, and OAB integration.

See `references/web-only-daily-report.md` for the **Web-only (SSH-forbidden) daily-report probe** playbook: the verified endpoint map for esggo.co / omniagent.esggo.co / live.esggo.co / memory.esggo.co, the Cloudflare-blocks-urllib-UA false-alarm trap, the OA-Twins `oab/broker.py --self-test` + `oa-twin-health.py` verification ritual, and the git-baseline lint-diff technique for proving a change added zero new violations.

See `references/crewai-oa-team-30.md` for the CrewAI OA-Team 30 swarm build/run mode: verified `load_crew()` JSON-first constraints (NO `description` in crew.jsonc; agents only standard fields; llm env-driven), `crewai run` `run_crew` failure workaround (`python main.py` / `uv run`), the GitHub Actions path pitfall (workflow MUST be at repo root `.github/workflows/`, not nested), the `CREWAI_API_KEY` secret-injection failure (length=0) blocker, and the OCI Always-Free AMD Micro `CannotParseRequest` 400 (console-UI-only) finding.

See `scripts/verify_oa_team_structure.py` for the no-network structure check (asserts 30 agents / 5 tasks / standard fields / `load_crew` assembles). Run it after any change to `oa-team-crewai/` so the "unverified" reminder is answered with fresh evidence. It does NOT claim LLM inference is verified — that needs a reachable endpoint.

### Web-only probe quick rules (2026-08-06 verified)

- Endpoints that actually exist: `esggo.co/api/health` (200, payload nested under `data`), `esggo.co/api/healthz` (**503** while env vars are un-injected), `omniagent.esggo.co/status` (the informative one: `active_workers`, `version`), `omniagent.esggo.co/agents` (401, needs `X-Omni-Token`). `omniagent.esggo.co/` `/api/*` `/metrics` `/healthz` are all 404 — stop probing them.
- **Python probes MUST send a browser `User-Agent`.** Bare `urllib.request.urlopen(url)` gets **403** from Cloudflare while `curl` on the same URL gets 200 — an un-UA'd health script manufactures false red lights.
- git-bash `curl -o /dev/null` returning **exit 23** is a local write quirk, not a service outage; `%{http_code}` is still valid.
- `broker.py --self-test` performs no asserts and always exits 0 — you must eyeball `received: ['health.heartbeat', 'swarm.phase']`. An empty list means OmniTag routing is silently dead.
- When a monitor's exit 1 points at a genuine 503, that is CORRECT behaviour. Report false-alarms-eliminated and real-anomalies-retained separately (「假警報 N→0、真異常 M 保留」); never report only the total dropping. Keep environment blockers (SSH forbidden, env not injected) distinct from task failure.
- For `.py` changes in this JS monorepo the relevant gate is `py_compile` + the module's own self-test + a ruff **baseline diff against `git show HEAD:<file>`** — not `pnpm test` (vitest never collects `.py`, and there is no pytest config). Put the baseline copy in a Windows-visible temp path; native `ruff` cannot resolve MSYS `/tmp/...` and answers `E902`.

## 9. VPS OOM recovery & OCI reboot (verified 2026-08-08)

The VPS is an **Oracle Always-Free ARM** instance (`161.118.248.180`, region `ap-singapore-1`,
~2.8G available RAM). Large models OOM it. The single hardest rule:

**Never load a model larger than ~2GB on the VPS.** `gemma4:e4b` = 9.6GB → OOM-kills the box,
SSH hangs at "Connection timed out during banner exchange", and the only unlock is an OCI reboot.
Use `gemma4:e2b` (~1.5GB) or `qwen2.5:3b` (~2GB) on the VPS. Keep `gemma4:latest` (custom modelfile,
RENDERER gemma4) on the LOCAL Windows Ollama — it is not replayable on VPS.

### OCI reboot from this environment (when SSH is dead)
Preferred: user reboots from OCI Console. If the user says "use oci CLI" / "DOING IT", reboot
programmatically. Verified working path:

- **OCI CLI binary is NOT preinstalled.** Install attempts that FAILED: `pip install oci-cli`
  (PyPI SSL `UNEXPECTED_EOF_WHILE_READING`), `uv pip install oci-cli` (hermes venv `yaml/_yaml.cp311`
  access-denied lock), `npm install @oracle/oci-sdk` (404 — package does NOT exist on npm),
  `npm install oci-sdk` (installs but `require('oci-sdk')` hangs >90s), OCI CLI MSI download (failed).
- **WORKING path:** call the OCI REST API directly with Python `cryptography` (present in hermes venv).
  Use a script like `_tmp_vps/oci_reboot.py`: read `C:/Users/dingj/.oci/config` with a MANUAL parser
  (`cp["DEFAULT"]` / `cp.defaults()` both return EMPTY under git-bash because of CRLF + `/c/` path
  issues — open the file and split lines instead; use Windows path `C:/Users/...` not `/c/Users/...`).
  Sign with RSA-SHA256, `Authorization: Signature ... keyId="<tenancy>/<user>/<fingerprint>"`,
  hit `GET /20160918/instances?compartmentId=<tenancy>` to find the OCID by public IP, then
  `POST /20160918/instances/<ocid>/actions/instanceAction?action=SOFTRESET`.
- **Known failure:** if the API returns **401**, the API key's fingerprint is not accepted by OCI
  console from this side — that is not a code bug, it is a credential/console-registration issue.
  At that point hand the user the single command (OCI Console → Reboot) and wait.

### Cloudflare WAF breaks Python httpx/urllib but NOT curl (verified 2026-08-08)
When a VPS container must call `gateway.esggo.co` (Cloudflare tunnel), `httpx`/`urllib` get **403
Forbidden** (TLS fingerprint blocked) while `curl` from the same container gets **200**. Fix: route
egress through `subprocess` curl, not httpx. Confirmed in the DeerFlow↔OAB bridge (`oab_sync.py`):
rewrote PUT/DELETE to shell out to `curl`. Also see `references/web-only-daily-report.md`
(Python probes need a browser User-Agent or Cloudflare 403s them too).

## 10. OmniBlueprint Hub + Speech-to-Speech voice-agent integration (verified 2026-08-08)

HUB = `omni-blueprint-hub` at `/opt/esggo/apps/omni-blueprint-hub` (native Node `monitor-server.mjs`,
pm2 :8787, live.esggo.co via nginx). It does text transcription + multi-lang translation SSE broadcast.
Adding voice: HuggingFace `speech-to-speech` (VAD→STT→LLM→TTS, OpenAI-Realtime-compatible WebSocket
`ws://host:8765/v1/realtime`). Integration shape:
- s2s runs on VPS; LLM slot points at local Ollama `gemma4:e2b` (NOT e4b — OOM, see §9).
- s2s text → HUB `/voice/bridge` endpoint → `/speak` → multi-lang SSE `/stream`.
- Resource budget on VPS (2.8G): Parakeet STT ~600MB + gemma4:e2b ~1.5GB + TTS ~400MB ≈ 2.6GB (tight).
- Plan + scripts committed to `esggo-learning-center`: `SPEECH_TO_SPEECH_INTEGRATION.md`,
  `_tmp_vps/ollama_downgrade.sh`, `_tmp_vps/deploy_voice_agent.sh`, `_tmp_vps/oci_reboot.py`.
- VPS-affected edits use `python3 - <<'PYEOF'` heredoc into `ssh ... 'python3 -'` (no local file copy
  needed). Node http server routes live in `monitor-server.mjs` `http.createServer` handler.

## 12. Cloudflare Worker deployment (deer-flow vision API)

When deploying a Cloudflare Worker for the deer-flow project:

1. **Vision model**: Ollama + Qwen3-VL-2B-Instruct (free, Apache 2.0, runs on CPU/GPU).
2. **Worker files**: All go in `/opt/esggo/deer-flow/` on the VPS.
3. **Architecture**: Edge endpoint → calls Ollama API at `localhost:11434` → returns structured JSON.
4. **OAB integration**: Worker publishes events with 5T tags (Traceable, Trackable, Tangible, Transparent, Trustworthy) to the OAB EventBus for OA-Team 30 swarm communication.
5. **Deployment**: `cd /opt/esggo/deer-flow && npm install && npx wrangler deploy`
6. **Model setup**: `winget install Ollama.Ollama && ollama serve && ollama pull qwen3-vl:2b`

Pitfall: The SSH terminal may be stuck in a reasoning loop (60/60 iterations). When this happens, do NOT retry terminal commands — dispatch a background subagent via `delegate_task` instead. The subagent creates all Worker files and runs deployment commands directly on the VPS. Monitor progress via the live transcript at `C:\Users\dingj\AppData\Local\hermes\cache\delegation\live\<delegation_id>\task-0.log`.

## Dependabot Alert Resolution (pnpm workspace overrides)

When Dependabot alerts are detected in the esggo repo:

1. Fetch alerts: `gh api repos/DingJun1028/esggo/dependabot/alerts?state=open\&per_page=100`
2. Parse with python3 to extract unique GHSA IDs, severity, package, fix version, vulnerable range
3. Categorize by severity: high/critical → immediate, medium → next sprint, low → backlog
4. Apply fixes via pnpm workspace overrides in `pnpm-workspace.yaml`
5. Run `pnpm install` to regenerate lockfile, then `pnpm audit --prod` to verify zero prod vulns
6. Run `pnpm run test` to verify no regressions
7. Commit with descriptive message referencing CVE/GHSA IDs
8. Push to main branch

### Key Rules

- Never use bare `>=` without upper bound — pnpm may jump across major versions.
- Never override transitive dev deps pinned by a parent dep (AGENTS.md #5).
- Document excluded deps with explicit rationale comments in pnpm-workspace.yaml (AGENTS.md #7).
- For transitive deps that cannot be upgraded directly (e.g., undici locked by jsdom@29), use workspace overrides.
- For dev-only deps blocked by parent (e.g., sharp <0.35.0 blocked by next@16), exclude with comment.
- `pnpm update <pkg>@<ver>` does NOT work for transitive dependencies — use overrides instead.
- `pnpm why <pkg>` may fail for transitive deps; grep the lockfile as an alternative.
- esggo-omni-center is remote-only (not cloned locally); use GitHub API/raw URLs to inspect.
- Main branch is `main`, not `master` — all gh api calls must use `?ref=main`.
- sharp stays at 0.34.5 because next@16 only accepts `^0.34.5` and it is devDep only (AGENTS.md #7).

### Override Syntax

```yaml
overrides:
  "undici": ">=7.29.0 <8"        # CVE-2026-13697 (GHSA-4cwx), CVSS 7.4
  "fast-uri": ">=3.1.5 <4"       # CVE-2026-18446 (GHSA-7p8r)
  "fast-xml-parser": ">=5.10.1"  # GHSA-8r6m
  "brace-expansion": ">=5.0.9"   # CVE-2026-69152 (GHSA-rgw5)
  "protobufjs": ">=7.6.5 <8"     # CVE-2026-59877 (GHSA-j3f2)
  "dompurify": ">=3.4.12 <4"
  "body-parser": ">=1.20.6 <2"
  "postcss": ">=8.5.23"
```

### Exclusions (with rationale)

- **sharp**: kept at current version because next@16 only accepts `^0.34.5`, and sharp is devDep only. Document in pnpm-workspace.yaml comments with AGENTS.md #7 reference.
- **hono**: not in root lockfile; transitive via learning-center only.

### Pitfalls

- `pnpm update <pkg>@<ver>` does NOT work for transitive dependencies — use overrides instead
- `pnpm why <pkg>` may fail for transitive deps; use `grep` in lockfile as alternative
- esggo-omni-center is remote-only (not cloned locally); use GitHub API/raw URLs to inspect
- Main branch is `main`, not `master` — all gh api calls must use `?ref=main`
- `sharp` stays at 0.34.5 because `next@16` only accepts `^0.34.5` and it is a devDep (AGENTS.md #7)
- `pnpm audit --prod` may show pre-existing failures unrelated to Dependabot alerts (e.g., Prisma module issues)
- Do NOT overwrite `pnpm-lock.yaml` with `git checkout` after making override changes — the lockfile must reflect the override resolution

### Override Syntax

```yaml
overrides:
  "undici": ">=7.29.0 <8"        # CVE-2026-13697 (GHSA-4cwx), CVSS 7.4
  "fast-uri": ">=3.1.5 <4"       # CVE-2026-18446 (GHSA-7p8r)
  "fast-xml-parser": ">=5.10.1"  # GHSA-8r6m
  "brace-expansion": ">=5.0.9"   # CVE-2026-69152 (GHSA-rgw5)
  "protobufjs": ">=7.6.5 <8"     # CVE-2026-59877 (GHSA-j3f2)
  "dompurify": ">=3.4.12 <4"
  "body-parser": ">=1.20.6 <2"
  "postcss": ">=8.5.23"
```

### Exclusions (with rationale)

- **sharp**: kept at current version because next@16 only accepts `^0.34.5`, and sharp is devDep only. Document in pnpm-workspace.yaml comments with AGENTS.md #7 reference.
- **hono**: not in root lockfile; transitive via learning-center only.

When deploying a Cloudflare Worker for the deer-flow project:

1. **Vision model**: Ollama + Qwen3-VL-2B-Instruct (free, Apache 2.0, runs on CPU/GPU).
2. **Worker files**: All go in `/opt/esggo/deer-flow/` on the VPS.
3. **Architecture**: Edge endpoint → calls Ollama API at `localhost:11434` → returns structured JSON.
4. **OAB integration**: Worker publishes events with 5T tags (Traceable, Trackable, Tangible, Transparent, Trustworthy) to the OAB EventBus for OA-Team 30 swarm communication.
5. **Deployment**: `cd /opt/esggo/deer-flow && npm install && npx wrangler deploy`
6. **Model setup**: `winget install Ollama.Ollama && ollama serve && ollama pull qwen3-vl:2b`

Pitfall: The SSH terminal may be stuck in a reasoning loop (60/60 iterations). When this happens, do NOT retry terminal commands — dispatch a background subagent via `delegate_task` instead. The subagent creates all Worker files and runs deployment commands directly on the VPS. Monitor progress via the live transcript at `C:\Users\dingj\AppData\Local\hermes\cache\delegation\live\<delegation_id>\task-0.log`.