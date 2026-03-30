# macmini

Bootstrap a fresh Mac Mini into a native [OpenClaw](https://openclaw.ai) machine.

Runs [thoughtbot/laptop](https://github.com/thoughtbot/laptop) first, then layers on OpenClaw-specific tools via `~/.laptop.local`.

## Install

```sh
git clone https://github.com/2n2-ai/macmini.git ~/macmini
cd ~/macmini
bash mac
```

The `mac` script:
1. Installs macOS software updates
2. Copies `laptop.local` to `~/.laptop.local`
3. Runs thoughtbot/laptop (Xcode CLT, Rosetta, Homebrew, git, gh, tmux, asdf, Node, Ruby)
4. laptop sources `~/.laptop.local`, which adds the OpenClaw layer

## What laptop.local adds

### CLI tools (Homebrew)
- [himalaya](https://github.com/pimalaya/himalaya) &mdash; email CLI (IMAP/SMTP)
- [cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/) &mdash; Cloudflare tunnels
- [flyctl](https://fly.io/docs/flyctl/) &mdash; Fly.io deployment
- [restic](https://restic.net) &mdash; encrypted backup
- [mise](https://mise.jdx.dev) &mdash; runtime version manager
- python3

### Applications (Homebrew Cask)
- [Google Chrome](https://www.google.com/chrome/) &mdash; browser automation via CDP
- [Tailscale](https://tailscale.com) &mdash; remote access

### Runtimes (via mise)
- Node.js 24
- Ruby (latest)

### Global npm packages
- [OpenClaw](https://openclaw.ai)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [pnpm](https://pnpm.io)
- [Vercel CLI](https://vercel.com/cli)

## The three-layer architecture

| Layer | What | Lifecycle | Strategy |
|-------|------|-----------|----------|
| **1. Environment** | Machine setup (this repo) | Reinstallable from scratch | `bash mac` |
| **2. Agent DNA** | Skills, persona, behaviors | Portable / white-label | `git clone` into workspace |
| **3. Instance** | Credentials, memory, projects | Company-specific, irreplaceable | Encrypted backup via restic |

## After bootstrap

```sh
# Configure gateway, channels, LaunchAgent
openclaw onboard --install-daemon

# (Optional) Restore instance from backup
bash ~/macmini/restore

# (Optional) Open Tailscale and sign in
```

## Backup and restore

```sh
# First backup (from your current machine)
brew install restic
export B2_ACCOUNT_ID="..." B2_ACCOUNT_KEY="..."
restic init --repo b2:openclaw-backup:/
restic backup ~/.openclaw \
  --exclude='workspace/projects/' \
  --exclude='extensions/*/node_modules/' \
  --exclude='browser/' \
  --tag openclaw

# Restore on new Mac Mini
bash ~/macmini/restore
```

## Why native instead of Docker?

| | Docker | Native Mac Mini |
|-|--------|-----------------|
| Browser | Headless only, `noSandbox` hack | Full headed Chrome via CDP |
| Camera | N/A | Native `camera.snap` |
| Screen recording | N/A | Native `screen.record` |
| Canvas | Separate HTTP server | Native WKWebView |
| Menu bar | N/A | Real-time status + badges |
| Persistence | Tools lost on restart | Everything persists |
| Remote access | Exposed ports | Tailscale Serve/Funnel |

## License

MIT
