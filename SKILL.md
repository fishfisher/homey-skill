---
name: homey
description: Control Homey smart home using the homeyctl CLI. Use when asked to control devices, trigger flows, check energy usage, manage zones/users, or interact with Athom Homey Pro. Covers device control (lights, thermostats, sensors), flow management (list/trigger/create), energy monitoring, insights, variables, and system operations.
metadata: {"clawdbot":{"emoji":"🏠","requires":{"bins":["homeyctl"]},"install":[{"id":"brew","kind":"brew","formula":"langtind/tap/homeyctl","bins":["homeyctl"],"label":"Install homeyctl (brew)"}]}}
---

# Homey

Control your Athom Homey Pro smart home hub using the `homeyctl` CLI. This skill enables device control, flow automation, energy monitoring, and system management through Homey's local API.

## Setup & Configuration

### Automatic Setup (Easiest)

If user can use browser:
```bash
homeyctl login
```

This handles authentication and discovery automatically.

### Manual Setup (For AI/Automation)

If user provides token from https://my.homey.app/ → Settings → API Keys:

1. **Get Homey hostname/IP:**
   - Ask user: "What is your Homey's IP address or hostname?"
   - User can find this in Homey app or on https://my.homey.app/ (Settings → System)
   - Format: IP like `192.168.1.100` or `.local` hostname like `homey-xxxxx.local`

2. **Configure:**
```bash
homeyctl config set-host <user-provided-host>
homeyctl config set-token <user-provided-token>
```

3. **Verify:**
```bash
homeyctl devices list
```

Configuration is stored in `~/Library/Application Support/homeyctl/config.toml` (macOS).

## Quick Start

```bash
# List all devices
homeyctl devices list

# Control a device
homeyctl devices set "Living Room Light" onoff true
homeyctl devices set "Living Room Light" dim 0.5

# Trigger a flow
homeyctl flows trigger "Good Morning"

# Check energy usage
homeyctl energy live
homeyctl energy report day
```

## Security: Token Scoping

**Important:** For AI assistants, use scoped tokens to prevent accidental changes.

The user should create a readonly token for you:
```bash
homeyctl token create "AI Bot" --preset readonly --no-save
```

Available presets:
- **readonly** - Safe for AI exploration (list/get only)
- **control** - Read + control devices, trigger flows
- **full** - Full access (same as owner)

If you try an operation without permissions, you'll see: `Error: 403 Missing Scopes`

## Core Capabilities

### 1. Device Control

List and control all connected smart home devices:

```bash
# List all devices (lights, sensors, thermostats, etc.)
homeyctl devices list

# Get device details (capabilities, state, zone)
homeyctl devices get "Living Room Light"

# Control devices by name
homeyctl devices set "Living Room Light" onoff true
homeyctl devices set "Living Room Light" dim 0.5
homeyctl devices set "Thermostat" target_temperature 22

# Delete a device
homeyctl devices delete "Old Device"
```

### 2. Flow Management

Manage Homey flows (automations):

```bash
# List all flows
homeyctl flows list

# Get flow details (use to see structure)
homeyctl flows get "My Flow"

# Trigger a flow by name
homeyctl flows trigger "Good Morning"

# Update existing flow (merge)
homeyctl flows update "My Flow" updated-flow.json

# Delete a flow
homeyctl flows delete "Old Flow"

# List available flow cards (for creating flows)
homeyctl flows cards --type trigger
homeyctl flows cards --type condition
homeyctl flows cards --type action
```

**Flow structure:** See `references/homeyctl-ai-context.md` for complete flow JSON format and examples.

### 3. Energy Monitoring

Track power usage and electricity prices:

```bash
# Live power usage (W)
homeyctl energy live

# Daily/weekly/monthly reports
homeyctl energy report day
homeyctl energy report week
homeyctl energy report month --date 2025-12

# Check/set electricity prices
homeyctl energy price                    # Show dynamic prices
homeyctl energy price set 0.50          # Set fixed price (kr/kWh)
homeyctl energy price type              # Show current type
homeyctl energy price type fixed        # Switch to fixed pricing
```

### 4. Insights & Historical Data

Access historical sensor data and logs:

```bash
# List all insight logs
homeyctl insights list

# Get historical data for a device
homeyctl insights get "homey:device:abc123:measure_power"

# Different time resolutions
homeyctl insights get "homey:device:abc123:measure_power" --resolution lastWeek
```

### 5. Variables

Manage logic variables used in flows:

```bash
# List all variables
homeyctl variables list

# Get/set variable value
homeyctl variables get "my_variable"
homeyctl variables set "my_variable" 42

# Create/delete variable
homeyctl variables create "new_var" number 0
homeyctl variables delete "new_var"
```

### 6. Zones & Users

Manage rooms and household members:

```bash
# List zones (rooms)
homeyctl zones list
homeyctl zones delete "Unused Room"

# List users (useful for flow conditions)
homeyctl users list
```

### 7. Apps & System

Manage installed apps and system operations:

```bash
# List installed apps
homeyctl apps list

# Restart an app
homeyctl apps restart com.some.app

# System information
homeyctl system info

# Reboot Homey (requires confirmation)
homeyctl system reboot
```

### 8. Notifications

Send notifications to Homey timeline:

```bash
# Send notification
homeyctl notifications send "Hello from CLI"

# List recent notifications
homeyctl notifications list
```

## Output Formats

```bash
# JSON output (default, machine-readable)
homeyctl devices list

# Table output (human-readable)
homeyctl devices list --format table

# Set default format globally
homeyctl config set-format table
```

## Resources

### references/homeyctl-ai-context.md

Complete documentation including:
- Full flow JSON format and schema
- Flow creation examples (simple and advanced)
- Device capability reference
- API token scoping details
- Detailed command reference

**Load this file when:**
- Creating or modifying flows
- Need detailed flow JSON examples
- Need device capability reference
- Troubleshooting API errors
