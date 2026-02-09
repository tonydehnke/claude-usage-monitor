# Claude Code Usage Monitor

Show your Claude Code 5-hour usage window — remaining time and utilization — in your terminal or status bar.

```
3:42 67%
```

Means: **3h 42m left** in the current window, **67% capacity used**.

## Two Options

| | `claude-usage.sh` | `ccstatusline-usage.sh` |
|---|---|---|
| **For** | Standalone use (tmux, Starship, terminal, etc.) | [ccstatusline](https://github.com/sirmalloc/ccstatusline) custom widget |
| **Platform** | macOS + Linux | macOS |
| **Dependencies** | `curl`, `jq` | `curl`, `jq`, ccstatusline |

Both scripts do the same thing — pick the one that fits your setup.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI (Pro or Max subscription)
- `curl` and `jq`

## Prerequisites: Connecting to Your Account

These scripts use the OAuth token that Claude Code creates when you log in. **No API key needed** — it uses your existing Claude Code session.

1. **Install Claude Code** if you haven't already: `npm install -g @anthropic-ai/claude-code`
2. **Log in** by running `claude` in your terminal and completing the login flow
3. That's it — the script reads the token automatically

**Where the token lives:**
- **macOS**: Stored in Keychain (under "Claude Code-credentials"). The script reads it with `security find-generic-password`.
- **Linux**: Stored in `~/.claude/credentials.json`. The script reads it with `jq`.

**Troubleshooting:**
- Getting `--:-- --%`? Make sure you've run `claude` at least once and completed login.
- Token expired? Just open Claude Code again — it refreshes automatically.
- Make sure `curl` and `jq` are installed: `brew install jq` (macOS) or `sudo apt install jq` (Linux).

---

## Option 1: Standalone (`claude-usage.sh`)

Works anywhere you can run a shell script. Cross-platform (macOS + Linux).

### Install

```bash
git clone https://github.com/tonydehnke/claude-usage-monitor.git
cd claude-usage-monitor
chmod +x claude-usage.sh

# Test it
./claude-usage.sh
```

### Status Bar Examples

**tmux** — add to `~/.tmux.conf`:

```bash
set -g status-right '#(~/.local/bin/claude-usage.sh)'
```

**Starship** — add to `~/.config/starship.toml`:

```toml
[custom.claude]
command = "~/.local/bin/claude-usage.sh"
when = true
format = "[$output]($style) "
style = "bold purple"
```

**i3bar / Waybar** — add as a custom script block.

**Terminal alias** — add to `~/.zshrc` or `~/.bashrc`:

```bash
alias cu='~/path/to/claude-usage.sh'
```

---

## Option 2: ccstatusline Widget (`ccstatusline-usage.sh`)

Use with [ccstatusline](https://github.com/sirmalloc/ccstatusline) as a custom-command widget inside Claude Code's status line.

### Install

```bash
# Copy the script
cp ccstatusline-usage.sh ~/.local/bin/
chmod +x ~/.local/bin/ccstatusline-usage.sh
```

### Configure ccstatusline

1. Run `ccstatusline` TUI to open the config editor
2. Add a **custom-command** widget
3. Set the command path to `~/.local/bin/ccstatusline-usage.sh`
4. Set timeout to `5000` (5 seconds)

Or manually add to `~/.config/ccstatusline/settings.json`:

```json
{
  "type": "custom-command",
  "color": "brightBlue",
  "commandPath": "~/.local/bin/ccstatusline-usage.sh",
  "timeout": 5000
}
```

Example full config with usage widget alongside other widgets:

```json
{
  "version": 3,
  "lines": [
    [
      { "type": "context-percentage", "color": "brightMagenta", "rawValue": true },
      { "type": "custom-command", "color": "brightBlue", "commandPath": "~/.local/bin/ccstatusline-usage.sh", "timeout": 5000 }
    ],
    [
      { "type": "model", "rawValue": true },
      { "type": "version", "rawValue": true }
    ]
  ]
}
```

---

## How It Works

1. Reads your Claude Code OAuth token from local credentials (macOS Keychain or `~/.claude/credentials.json`)
2. Calls Anthropic's usage API to get 5-hour sliding window status
3. Displays remaining time + utilization percentage
4. Caches results for 3 minutes to avoid excessive API calls

**Your token never leaves your machine** — it's only used to call the official Anthropic API directly.

## Output

| Output | Meaning |
|--------|---------|
| `3:42 67%` | 3h 42m remaining, 67% used |
| `0:00 100%` | Window expired / fully used |
| `--:-- --%` | Can't reach API or not logged in |

## License

MIT
