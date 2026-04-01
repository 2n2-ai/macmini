# macmini

Bootstrap a fresh Mac Mini into a native [OpenClaw](https://openclaw.ai) machine.

Runs [thoughtbot/laptop](https://github.com/thoughtbot/laptop) first, then layers on OpenClaw-specific tools via `~/.laptop.local`.

## Install

Before running, upgrade to the latest macOS via **System Settings > General > Software Update**.

```sh
curl -fsSL https://raw.githubusercontent.com/2n2-ai/macmini/main/mac | bash
```

Then open a new terminal and run the setup wizard:

```sh
git clone https://github.com/2n2-ai/macmini.git ~/macmini
bash ~/macmini/setup
```

## The three layers

| Layer | What | Repo | Command |
|-------|------|------|---------|
| **1. Environment** | Machine setup | `2n2-ai/macmini` (public) | `bash mac` |
| **2. Agent DNA** | Skills, persona, behaviors | `2n2-ai/eli` (private) | `dna status` |
| **3. Instance** | Company identity, memory, credentials | `2n2-ai/l3` (private) | `instance status` |

The `setup` wizard walks you through cloning or creating L2 and L3 repos, importing secrets, and starting the gateway.

## How the workspace works

Both L2 and L3 are [bare git repos](https://www.atlassian.com/git/tutorials/dotfiles) sharing `~/.openclaw/workspace/` as their work tree. No `.git` directory inside the workspace.

```sh
# Agent DNA (portable, white-labelable)
dna add skills/new-skill/
dna commit -m "Add skill"
dna push

# Instance (company-specific)
instance add memory/2026-03-30.md
instance commit -m "Daily notes"
instance push
```

## What laptop.local adds

### CLI tools (Homebrew)
- himalaya, cloudflared, flyctl, python3, mise

### Applications (Homebrew Cask)
- Google Chrome, Tailscale

### Runtimes (via mise)
- Node.js 24, Ruby (latest)

### Global npm packages
- OpenClaw, Claude Code, pnpm, Vercel CLI

## Migrating to a new Mac Mini

On the **old machine**:
```sh
bash ~/macmini/bundle-secrets
# Transfer secrets-bundle.zip to new machine
```

On the **new Mac Mini**:
```sh
curl -fsSL https://raw.githubusercontent.com/2n2-ai/macmini/main/mac | bash
# Open new terminal
git clone https://github.com/2n2-ai/macmini.git ~/macmini
bash ~/macmini/setup
# The wizard handles: gh auth, L2 clone, L3 clone, secrets import, gateway install
```

## Post-install: Browser

The browser plugin is a bundled OpenClaw plugin that must be explicitly enabled. The `setup` wizard handles this automatically, but if you need to troubleshoot:

```bash
openclaw browser status
```

If you see `unknown method: browser.request` or `pairing required`, see [docs/browser-setup.md](docs/browser-setup.md).

## Why native instead of Docker?

| | Docker | Native Mac Mini |
|-|--------|-----------------|
| Browser | Headless only, `noSandbox` hack | Full headed Chrome via CDP |
| Camera | N/A | Native `camera.snap` |
| Screen recording | N/A | Native `screen.record` |
| Canvas | Separate HTTP server | Native WKWebView |
| Persistence | Tools lost on restart | Everything persists |
| Remote access | Exposed ports | Tailscale Serve/Funnel |

## License

MIT
