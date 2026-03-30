# macmini

Bootstrap a fresh Mac Mini into a fully operational native [OpenClaw](https://openclaw.ai) machine.

Inspired by [thoughtbot/laptop](https://github.com/thoughtbot/laptop). One script. Idempotent. No Docker.

## Why native?

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

### Version management

- [asdf](https://asdf-vm.com) with Node.js plugin
- Node.js 24 (OpenClaw requires >= 22.14)

### Global tools (npm)

- [OpenClaw](https://openclaw.ai) &mdash; AI gateway and personal assistant platform
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) &mdash; Anthropic's CLI for Claude
- [pnpm](https://pnpm.io) &mdash; fast package manager

### CLI tools (Homebrew)

- git
- [gh](https://cli.github.com) (GitHub CLI)
- jq
- tmux

### Applications (Homebrew Cask)

- [Google Chrome](https://www.google.com/chrome/) &mdash; browser automation via CDP
- [Tailscale](https://tailscale.com) &mdash; remote access

### What it does NOT install

- Docker (the entire point is native macOS)
- Any IDE or editor (use `~/.macmini.local`)
- API keys or credentials (handled by `openclaw onboard`)

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

## After bootstrap

1. **Open a new terminal** to pick up shell changes.

2. **Run OpenClaw onboarding:**

   ```sh
   openclaw onboard --install-daemon
   ```

   This configures model providers, workspace, gateway, messaging channels,
   and installs a LaunchAgent so the gateway starts on boot.

3. **(Optional) Set up Tailscale** for remote access. Open it from Applications and sign in.

4. **(Optional) Grant macOS permissions** when prompted &mdash; Screen Recording, Camera, Accessibility, Notifications, etc. via System Settings > Privacy & Security.

## Customize

Create `~/.macmini.local` to add your own steps. It runs at the end of the bootstrap:

```sh
# ~/.macmini.local

brew install --cask brave-browser
brew install --cask visual-studio-code
brew install mise
brew install imagemagick
```

## Debugging

The script logs everything to `~/macmini.log`.

```sh
less ~/macmini.log
```

## License

MIT
