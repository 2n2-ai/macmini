# macmini

Bootstrap a fresh Mac Mini into a fully operational native [OpenClaw](https://openclaw.ai) machine.

Inspired by [thoughtbot/laptop](https://github.com/thoughtbot/laptop). Batteries included. No Docker.

## The three-layer architecture

An OpenClaw deployment has three independent layers. Each can be installed, migrated, and version-controlled separately.

### Layer 1: Environment (this repo)

The machine itself. Chrome, Node, CLI tools, runtimes. Reinstallable from scratch in minutes. The `mac` script handles this.

### Layer 2: Agent DNA (separate repo)

The skills, persona, and behaviors that make your agent *your agent*. SOUL.md, IDENTITY.md, sub-agent definitions, 24+ skills. Portable across machines and white-label deployments. Fork it, swap the persona, deploy to a new customer.

### Layer 3: Instance (encrypted backup)

Company-specific data: credentials, memory, projects, conversation history. Backed up encrypted to Backblaze B2 via restic. The `restore` script pulls it down to any new machine.

## Why native instead of Docker?

| | Docker | Native Mac Mini |
|-|--------|-----------------|
| Browser | Headless only, `noSandbox` hack, Chromium baked into image | Full headed Chrome/Brave via CDP, multiple profiles, real sessions |
| Camera | Not available | Native `camera.snap`, `camera.clip` |
| Screen recording | Not available | Native `screen.record` |
| Canvas | Requires separate HTTP server | Native WKWebView panel |
| Menu bar app | Not applicable | Real-time activity, status badges, health monitoring |
| Voice wake | Not available | Native speech recognition |
| TCC permissions | Container permissions only | Full macOS permission model (9 types) |
| Persistence | Tools lost on container restart | Everything persists naturally |
| Remote access | VPN/SSH/exposed ports | Tailscale Serve/Funnel with identity-based auth |

## What it installs

### macOS prerequisites

- Xcode Command Line Tools
- Rosetta 2 (Apple Silicon)

### Package management

- [Homebrew](https://brew.sh)

### Runtimes (via [mise](https://mise.jdx.dev))

- Node.js 24 (OpenClaw requires >= 22.14)
- Ruby (latest, for Rails projects)

### Global tools (npm)

- [OpenClaw](https://openclaw.ai) &mdash; AI gateway and personal assistant platform
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) &mdash; Anthropic's CLI for Claude
- [pnpm](https://pnpm.io) &mdash; fast package manager
- [Vercel CLI](https://vercel.com/cli) &mdash; deployment

### CLI tools (Homebrew)

- git, [gh](https://cli.github.com), jq, tmux
- [himalaya](https://github.com/pimalaya/himalaya) &mdash; email CLI (IMAP/SMTP)
- [cloudflared](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/) &mdash; Cloudflare tunnels
- [flyctl](https://fly.io/docs/flyctl/) &mdash; Fly.io deployment
- [restic](https://restic.net) &mdash; encrypted backup
- python3

### Applications (Homebrew Cask)

- [Google Chrome](https://www.google.com/chrome/) &mdash; browser automation via CDP
- [Tailscale](https://tailscale.com) &mdash; remote access

### What it does NOT install

- Docker (the entire point is native macOS)
- Any IDE or editor (use `~/.macmini.local`)
- API keys or credentials (handled by `openclaw onboard` or `restore`)

## Install

Download and review the script:

```sh
curl -fsSL https://raw.githubusercontent.com/openclaw/macmini/main/mac | less
```

Then run it:

```sh
curl -fsSL https://raw.githubusercontent.com/openclaw/macmini/main/mac -o /tmp/mac
bash /tmp/mac
```

Or clone and run:

```sh
git clone https://github.com/openclaw/macmini.git
cd macmini
bash mac
```

## Full setup flow

```sh
# 1. Environment (Layer 1)
bash mac

# 2. Agent DNA (Layer 2)
git clone <your-agent-dna-repo> ~/.openclaw/workspace

# 3. Instance restore (Layer 3) — if migrating from an existing machine
bash restore

# 4. Configure gateway + LaunchAgent
openclaw onboard --install-daemon
```

For a **white-label customer**, skip step 3 and run onboarding fresh with their credentials.

## Backup and restore

The `restore` script pulls down an encrypted backup from Backblaze B2:

```sh
bash restore
```

It prompts for your restic password and B2 credentials (or reads them from macOS Keychain), restores `~/.openclaw/`, re-clones projects from a manifest, and reinstalls extensions.

### Setting up backups on your current machine

```sh
# Install restic (already included in mac script)
brew install restic

# Initialize the backup repository
export B2_ACCOUNT_ID="your-b2-id"
export B2_ACCOUNT_KEY="your-b2-key"
restic init --repo b2:openclaw-backup:/

# Run a backup
restic backup ~/.openclaw \
  --exclude='workspace/projects/' \
  --exclude='extensions/*/node_modules/' \
  --exclude='openclaw.json.backup.*' \
  --exclude='openclaw.json.bak*' \
  --exclude='browser/' \
  --exclude='delivery-queue/' \
  --tag openclaw

# Prune old snapshots
restic forget --keep-daily 7 --keep-weekly 4 --keep-monthly 6 --prune
```

Automate with a LaunchAgent for scheduled backups every 6 hours.

## Customize

Create `~/.macmini.local` to add your own steps. It runs at the end of the bootstrap:

```sh
# ~/.macmini.local

brew install --cask brave-browser
brew install --cask visual-studio-code
brew install imagemagick
```

## Debugging

The script logs everything to `~/macmini.log`.

```sh
less ~/macmini.log
```

## License

MIT
