---
name: homey
description: Control Homey smart home using the homeyctl CLI. Use when asked to control devices, trigger flows, check energy usage, manage zones/users, moods, presence, weather, or interact with Athom Homey Pro. Covers device control (lights, thermostats, sensors), flow management (list/trigger/create/folders), energy monitoring, insights, variables, system operations, dashboards, moods, presence tracking, and weather.
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
# Get complete system snapshot
homeyctl snapshot
homeyctl snapshot --include-flows

# List all devices
homeyctl devices list

# Control a device (multiple ways)
homeyctl devices on "Living Room Light"      # Shortcut
homeyctl devices off "Living Room Light"     # Shortcut
homeyctl devices set "Living Room Light" dim 0.5

# Trigger a flow
homeyctl flows trigger "Good Morning"

# Activate a mood
homeyctl moods set "Movie Time"

# Check presence and weather
homeyctl presence get me
homeyctl weather current

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
homeyctl devices list --filter "living"  # Filter by name pattern

# Get device details (capabilities, state, zone)
homeyctl devices get "Living Room Light"

# Control devices (multiple ways)
homeyctl devices on "Living Room Light"           # Shortcut (turns on)
homeyctl devices off "Living Room Light"          # Shortcut (turns off)
homeyctl devices set "Living Room Light" dim 0.5  # Set specific capability
homeyctl devices set "Thermostat" target_temperature 22

# Device management
homeyctl devices rename "Old Name" "New Name"
homeyctl devices move "Device" "Kitchen"          # Move to zone
homeyctl devices hide "Device"                    # Hide from views
homeyctl devices delete "Old Device"

# Device settings (separate from capabilities)
homeyctl devices settings get "Device"
homeyctl devices settings set "Device" setting_name value
```

### 2. Flow Management

Manage Homey flows (automations):

```bash
# List all flows
homeyctl flows list
homeyctl flows list --filter "morning"  # Filter by name pattern

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

# Flow folders (organize flows)
homeyctl flows folders list
homeyctl flows folders create "Automation"
homeyctl flows folders get "Automation"
homeyctl flows folders update "Automation" updated-folder.json
homeyctl flows folders delete "Old Folder"
```

**Flow structure:** See `references/homeyctl-ai-context.md` for complete flow JSON format and examples.

### 3. Energy Monitoring

Track power usage and electricity prices:

```bash
# Live power usage (W)
homeyctl energy live

# Daily/weekly/monthly/yearly reports
homeyctl energy report day
homeyctl energy report week
homeyctl energy report month --date 2025-12
homeyctl energy report year --date 2025

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

# Delete insight log
homeyctl insights delete "homey:device:abc123:measure_power"
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
# Zones (rooms)
homeyctl zones list
homeyctl zones create "New Room"
homeyctl zones get "Living Room"
homeyctl zones update "Living Room" updated-zone.json
homeyctl zones delete "Unused Room"

# Users (household members)
homeyctl users list
homeyctl users get "User Name"
homeyctl users create "New User" user-data.json
homeyctl users update "User" updated-user.json
homeyctl users delete "Old User"
```

### 7. Apps & System

Manage installed apps and system operations:

```bash
# App management
homeyctl apps list
homeyctl apps get com.some.app
homeyctl apps install com.some.app
homeyctl apps uninstall com.some.app
homeyctl apps enable com.some.app
homeyctl apps disable com.some.app
homeyctl apps restart com.some.app
homeyctl apps settings get com.some.app
homeyctl apps settings set com.some.app setting_name value

# System information and control
homeyctl system info
homeyctl system name                    # Get system name
homeyctl system name set "My Homey"    # Set system name
homeyctl system reboot                 # Reboot Homey (requires confirmation)
```

### 8. Notifications

Send notifications to Homey timeline:

```bash
# Send notification
homeyctl notifications send "Hello from CLI"

# List recent notifications
homeyctl notifications list

# Delete notification
homeyctl notifications delete <notification-id>
```

### 9. Moods

Manage and activate room moods (lighting scenes):

```bash
# List all moods
homeyctl moods list

# Get mood details
homeyctl moods get "Movie Time"

# Activate a mood
homeyctl moods set "Movie Time"

# Create new mood
homeyctl moods create mood-data.json

# Update mood
homeyctl moods update "Movie Time" updated-mood.json

# Delete mood
homeyctl moods delete "Old Mood"
```

### 10. Presence Tracking

Track user presence (home/away) and sleep status:

```bash
# Get user presence
homeyctl presence get me
homeyctl presence get "User Name"

# Set presence (home/away)
homeyctl presence set me home
homeyctl presence set me away
homeyctl presence set "User Name" home

# Get sleep status
homeyctl presence asleep get me
homeyctl presence asleep get "User Name"

# Set sleep status
homeyctl presence asleep set me true
homeyctl presence asleep set me false
```

### 11. Weather

Get current weather and forecasts from Homey:

```bash
# Current weather conditions
homeyctl weather current

# Hourly forecast
homeyctl weather forecast
```

### 12. Dashboards

Manage Homey dashboards:

```bash
# List all dashboards
homeyctl dashboards list

# Get dashboard details
homeyctl dashboards get "Main Dashboard"

# Create dashboard
homeyctl dashboards create dashboard-data.json

# Update dashboard
homeyctl dashboards update "Dashboard" updated-dashboard.json

# Delete dashboard
homeyctl dashboards delete "Old Dashboard"
```

### 13. Snapshot

Get complete Homey system state in one call (useful for AI context):

```bash
# Get system snapshot (devices, zones, users, etc.)
homeyctl snapshot

# Include flows in snapshot
homeyctl snapshot --include-flows

# Table format for quick overview
homeyctl snapshot --format table
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
