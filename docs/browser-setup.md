# Browser Plugin Setup

The OpenClaw browser is a **bundled plugin** that must be enabled in `openclaw.json`. Without it, `openclaw browser`, the browser tool, and `browser.request` gateway method are all unavailable.

## Problem

On a fresh install, `plugins.entries.browser` may be missing from `openclaw.json`. When this happens:

```
$ openclaw browser status
GatewayClientRequestError: unknown method: browser.request
```

You may also see a `pairing required` error if the CLI's device identity hasn't been approved by the gateway.

## Fix

### 1. Enable the browser plugin

Add `browser` to `plugins.entries` in `~/.openclaw/openclaw.json`:

```json
{
  "plugins": {
    "entries": {
      "browser": {
        "enabled": true
      }
    }
  }
}
```

Or via the CLI:

```bash
openclaw config set plugins.entries.browser.enabled true
```

**⚠️ Never overwrite `plugins.entries` or `plugins.allow` entirely.** Always add/modify individual keys. Overwriting silently disables all other plugins (Telegram, Discord, Slack) with zero warnings in the logs.

### 2. Restart the gateway

```bash
openclaw gateway restart
```

### 3. Approve device pairing (if needed)

If `openclaw browser status` returns `pairing required`:

```bash
openclaw devices list          # Check for pending requests
openclaw devices approve --latest   # Approve the pending device
```

### 4. Verify

```bash
openclaw browser status
openclaw browser open https://example.com
openclaw browser screenshot
```

## Configuration options

```bash
# Use a specific Chrome/Chromium binary
openclaw config set browser.executablePath "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"

# Headed mode (recommended for Mac Mini — you can see what the agent sees)
openclaw config set browser.headless false

# Disable the sandbox (only needed in Docker/container environments)
openclaw config set browser.noSandbox true
```

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| `unknown method: browser.request` | Browser plugin not in config | Add `plugins.entries.browser.enabled: true`, restart gateway |
| `pairing required` | CLI device not approved | `openclaw devices approve --latest` |
| Browser launches but agent can't use it | Plugin disabled at runtime | Check `openclaw config get plugins.entries.browser` |
| `browser.request` works but pages timeout | Chrome not installed | Install via `brew install --cask google-chrome` |
