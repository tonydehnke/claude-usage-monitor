# Claude Code Usage Monitor

Show your Claude Code 5-hour usage window — remaining time and utilization — in your terminal or status bar.

```
$ ./claude-usage.sh
3:42 67%
```

Means: **3h 42m left** in the current window, **67% capacity used**.

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI, logged in
- `curl` and `jq`
- macOS or Linux

## Install

```bash
# Clone or download
git clone https://github.com/YOUR_USERNAME/claude-usage-monitor.git
cd claude-usage-monitor
chmod +x claude-usage.sh

# Test it
./claude-usage.sh
```

## How It Works

1. Reads your Claude Code OAuth token from your local credentials (macOS Keychain or credentials file)
2. Calls Anthropic's usage API to get your 5-hour sliding window status
3. Displays remaining time + utilization percentage
4. Caches results for 3 minutes to avoid excessive API calls

**Your token never leaves your machine** — it's only used to call the official Anthropic API directly.

## Status Bar Integration

### tmux

Add to `~/.tmux.conf`:

```bash
set -g status-right '#(~/.local/bin/claude-usage.sh)'
```

### Starship

Add to `~/.config/starship.toml`:

```toml
[custom.claude]
command = "~/.local/bin/claude-usage.sh"
when = true
format = "[$output]($style) "
style = "bold purple"
```

### i3bar / Waybar

Add as a custom script block pointing to `claude-usage.sh`.

### Quick Terminal Check

```bash
# Add alias to ~/.zshrc or ~/.bashrc
alias cu='~/path/to/claude-usage.sh'
```

## Output

| Output | Meaning |
|--------|---------|
| `3:42 67%` | 3h 42m remaining, 67% used |
| `0:00 100%` | Window expired / fully used |
| `--:-- --%` | Can't reach API or not logged in |

## Configuration

Edit the top of `claude-usage.sh` to adjust:

- `CACHE_TTL=180` — how long to cache results (seconds)
- `LOCK_TTL=30` — minimum time between API calls (seconds)

## License

MIT
