<p align="center">
  <a href="https://jojoml.github.io/tools/codex-accounts-demo.html">
    <img src="https://jojoml.github.io/tools/codex-accounts-demo-preview.gif" alt="Codex Account Manager demo preview" width="100%">
  </a>
</p>

<p align="center">
  <a href="https://jojoml.github.io/tools/codex-accounts-demo.html">Watch the full demo</a>
</p>

# Codex Account Manager

Switch between multiple Codex accounts without wiping your local Codex history.

`codex-accounts` only swaps `~/.codex/auth.json`. Your sessions, logs, memories, config, and the rest of `~/.codex` stay in place.

## Install

Interactive install:

```bash
curl -fsSL https://jojoml.github.io/tools/codex-accounts.sh | bash
```

The installer will:

- download the latest `codex-accounts`
- install it to `~/.local/bin/codex-accounts`
- create `~/.local/bin` if needed
- make it executable
- ask whether to add `~/.local/bin` to `bash` or `zsh`

Non-interactive install:

```bash
curl -fsSL https://jojoml.github.io/tools/codex-accounts.sh | bash -s -- --shell zsh
curl -fsSL https://jojoml.github.io/tools/codex-accounts.sh | bash -s -- --shell bash
```

## Quick Start

```bash
# Save the account you are currently using
codex login
codex-accounts save personal

# Add another account
codex-accounts add work
codex login
codex-accounts save work

# Switch any time
codex-accounts switch personal
codex-accounts switch work

# See usage for all saved accounts
codex-accounts usage
```

## Why Use It

- keep your local Codex history and state
- switch accounts by name instead of logging in from scratch each time
- see per-account Codex usage snapshots in one place

## Commands

```bash
codex-accounts list
codex-accounts current
codex-accounts save <name>
codex-accounts add <name>
codex-accounts switch <name>
codex-accounts usage
```

## Important: File-Based Auth

This tool only manages `~/.codex/auth.json`.

If Codex is using the OS keychain/keyring, there may be no `auth.json` file to switch. In that case, set Codex to file-based credentials:

```toml
# ~/.codex/config.toml
cli_auth_credentials_store = "file"
```

Then run:

```bash
codex login
```

## What It Changes

When you switch accounts, it only touches:

- `~/.codex/auth.json`
- `~/.codex-switch/state`

Everything else under `~/.codex` stays where it is, including:

- `history.jsonl`
- `sessions/`
- `log/`
- `memories/`
- `config.toml`

## Requirements

- macOS or Linux
- Codex CLI installed
- `jq` for `codex-accounts usage`

If you do not have Codex CLI yet:

```bash
brew install codex
```

## Notes

- saved accounts are stored as plain `auth.json` copies under `~/codex-data`
- protect `~/codex-data` like any other credential store
- if a saved account shows an expired token in `usage`, log into that account again and run `codex-accounts save <name>`
