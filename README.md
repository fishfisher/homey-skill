# Homey Skill 🏠

An LLM assistant skill for controlling Athom Homey Pro smart home using the `homeyctl` CLI.

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

### 2. Configure homeyctl

#### Interactive login (recommended for manual setup)

```bash
homeyctl login
```

This will open a browser for authentication with Homey.

#### Token-based authentication (recommended for LLM assistants and non-interactive mode)

For automated setups or LLM assistants where browser interaction isn't available, use token-based authentication:

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

## License

MIT
