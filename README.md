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

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI, logged in
- `curl` and `jq`

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
