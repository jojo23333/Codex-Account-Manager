# Codex Account Manager

Codex Account Manager is a lightweight multi-account switcher for Codex CLI that preserves your local `~/.codex` history and state.

Inspired by [bashar94/codex-cli-account-switcher](https://github.com/bashar94/codex-cli-account-switcher.git).

The `codex-accounts` command is intentionally lightweight:

- It only swaps `~/.codex/auth.json`
- It preserves your `history.jsonl`, `sessions/`, logs, memories, config, and the rest of `~/.codex`
- It keeps a small state file so you can see the current and previous account
- It can fetch each saved account's Codex usage snapshot

## Why This Exists

Codex CLI does not currently provide built-in multi-account switching. If you use multiple OpenAI accounts, the usual fallback is to re-login each time or manually move files around.

This script gives you a simple shell workflow:

- save the current login as a named account
- add a new account
- switch between saved accounts
- keep your Codex history and local state intact

## Important Auth Note

This switcher only manages `~/.codex/auth.json`.

If Codex is storing credentials in the OS keyring instead of `auth.json`, switching will not work until you tell Codex to use file-based auth:

```toml
# ~/.codex/config.toml
cli_auth_credentials_store = "file"
```

After adding that line, run:

```bash
codex login
```

## Installation

```bash
git clone https://github.com/bashar94/codex-cli-account-switcher.git
cd codex-cli-account-switcher
chmod +x codex-accounts

# Optional
sudo mv codex-accounts /usr/local/bin/
```

## Commands

```bash
codex-accounts list
codex-accounts current
codex-accounts save <name>
codex-accounts add <name>
codex-accounts switch <name>
codex-accounts usage
```

### What Each Command Does

- `list`: show all saved accounts from `~/codex-data`
- `current`: show the current and previous account from `~/.codex-switch/state`
- `save <name>`: save the current `auth.json` as `~/codex-data/<name>.json`
- `add <name>`: prepare a clean login for a new account, then ask you to run `codex login`
- `switch <name>`: back up the current `auth.json`, then activate the saved account
- `usage`: show each saved account's 5-hour and weekly Codex usage in a single-column card list

## Typical Flow

```bash
# Save the account you are currently logged into
codex login
codex-accounts save personal

# Add another account
codex-accounts add work
codex login
codex-accounts save work

# Switch back and forth
codex-accounts switch personal
codex-accounts switch work

# See each account's current usage snapshot
codex-accounts usage
```

## What Gets Changed

When you switch accounts, the script only touches:

- `~/.codex/auth.json`
- `~/.codex-switch/state`

Everything else under `~/.codex` stays in place, including:

- `history.jsonl`
- `sessions/`
- `log/`
- `memories/`
- `config.toml`
- any other Codex local state

That means your Codex history is preserved instead of being replaced on every account switch.

## Usage Summary

`codex-accounts usage` runs each saved account inside an isolated temporary `HOME`, calls Codex's own `account/read` and `account/rateLimits/read` endpoints, and renders the returned 5-hour and weekly windows as one card per row.

Notes about `usage`:

- it uses Codex directly instead of parsing local session logs
- it is read-only: it does not touch your active `~/.codex/auth.json` and it does not rewrite saved account files
- the cards label the two Codex windows as `5h limit` and `Weekly limit`
- it requires `jq`, Codex CLI, and network access
- if a saved account reports an expired token, log into that account again and run `codex-accounts save <name>`

## Data Layout

| Path | Purpose |
| --- | --- |
| `~/.codex/auth.json` | Active Codex login |
| `~/codex-data/<name>.json` | Saved auth file for an account |
| `~/.codex-switch/state` | Stores `CURRENT` and `PREVIOUS` |

## Requirements

- macOS or Linux
- `bash`
- `jq`
- Codex CLI installed
- network access for `codex-accounts usage`

Install Codex CLI if needed:

```bash
brew install codex
```

## Notes

- If `~/.codex/auth.json` is missing, you probably need to run `codex login`
- If this is your first time using the switcher, it may ask you to name the currently logged-in account
- Saved accounts are plain `auth.json` copies, so protect `~/codex-data` like any other credential store
