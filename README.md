# Homey Skill 🏠

A Clawdbot skill for controlling Athom Homey Pro smart home using the `homeyctl` CLI.

## Features

- 🔌 Device control (lights, thermostats, sensors)
- 🤖 Flow management (list, trigger, update, delete)
- ⚡ Energy monitoring and reporting
- 📊 Insights and historical data
- 🏠 Zone and user management
- 📱 Notifications and variables

## Installation

### 1. Install homeyctl

```bash
brew tap langtind/tap
brew install homeyctl
```

### 2. Install skill

```bash
cd ~/clawd/skills
git clone https://github.com/fishfisher/homey-skill.git homey
```

### 3. Configure homeyctl

Get token from [my.homey.app](https://my.homey.app/) → Settings → API Keys

```bash
homeyctl config set-host <your-homey-ip-or-hostname>
homeyctl config set-token <your-token>
```

Verify:
```bash
homeyctl devices list
```

## Usage

Once installed, the skill enables natural language control of your Homey:

- "Turn on living room lights"
- "What's the temperature in the bedroom?"
- "Trigger my good morning flow"
- "Show energy usage today"

## Resources

- [homeyctl GitHub](https://github.com/langtind/homeyctl)
- [Homey Developer Docs](https://homey.app/developer/)
- [Clawdbot Documentation](https://github.com/cased/clawdbot)

## License

MIT
