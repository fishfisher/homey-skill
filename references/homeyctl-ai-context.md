# homeyctl - AI Assistant Context

## Overview
CLI for controlling Homey smart home via local API.

## IMPORTANT: Scoped Tokens for AI Bots

AI assistants should use **restricted tokens** to prevent accidental damage.

### Creating a Scoped Token

The user (human) should run this command to create a token for you:
```bash
homeyctl token create "AI Bot" --preset readonly --no-save
```

This outputs a token that only has READ access. The AI cannot:
- Control devices
- Delete devices/flows/zones
- Trigger flows
- Modify anything

### Available Presets

| Preset | Access Level | Use Case |
|--------|--------------|----------|
| readonly | Read only | Safe for AI exploration |
| control | Read + control | AI can control devices, trigger flows |
| full | Full access | Same as owner (dangerous) |

### Using the Token

After creating, configure homeyctl with the scoped token:
```bash
homeyctl config set-token <the-token-from-above>
```

### Verifying Your Access Level

If you try an operation you don't have access to, you'll get:
```
Error: 403 Missing Scopes
```

This is expected behavior with a readonly token.

## Quick Setup (Full Access)

For users who want full access:
```bash
homeyctl login
```

## Available Commands

### Devices
```bash
homeyctl devices list                  # List all devices
homeyctl devices list --filter "name"  # Filter by name pattern
homeyctl devices get <id>              # Get device details
homeyctl devices on <name-or-id>       # Turn device on (shortcut)
homeyctl devices off <name-or-id>      # Turn device off (shortcut)
homeyctl devices set <id> <capability> <value>  # Control device
homeyctl devices rename <name> <new-name>  # Rename a device
homeyctl devices move <name> <zone>    # Move device to zone
homeyctl devices hide <name-or-id>     # Hide device from views
homeyctl devices delete <name-or-id>   # Delete a device
homeyctl devices settings get <id>     # Get device settings
homeyctl devices settings set <id> <setting> <value>  # Set device setting
```

### Flows
```bash
homeyctl flows list                    # List all flows
homeyctl flows list --filter "name"    # Filter by name pattern
homeyctl flows get <name-or-id>        # Get flow details
homeyctl flows create <file.json>      # Create flow from JSON
homeyctl flows update <name> <file>    # Update existing flow (merge)
homeyctl flows trigger <name-or-id>    # Trigger a flow by name or ID
homeyctl flows delete <name-or-id>     # Delete a flow
homeyctl flows cards --type <type>     # List flow cards (trigger/condition/action)

# Flow folders
homeyctl flows folders list            # List all flow folders
homeyctl flows folders get <name>      # Get folder details
homeyctl flows folders create <name>   # Create flow folder
homeyctl flows folders update <name> <file>  # Update folder
homeyctl flows folders delete <name>   # Delete folder
```

### Zones & Users
```bash
homeyctl zones list                    # List all zones
homeyctl zones get <name-or-id>        # Get zone details
homeyctl zones create <name>           # Create a zone
homeyctl zones update <name> <file>    # Update zone
homeyctl zones delete <name-or-id>     # Delete a zone

homeyctl users list                    # List all users
homeyctl users get <name-or-id>        # Get user details
homeyctl users create <name> <file>    # Create user
homeyctl users update <name> <file>    # Update user
homeyctl users delete <name-or-id>     # Delete user
```

### Presence & Moods
```bash
# Presence tracking
homeyctl presence get <user>           # Get presence status
homeyctl presence set <user> <home|away>  # Set presence
homeyctl presence asleep get <user>    # Get sleep status
homeyctl presence asleep set <user> <true|false>  # Set sleep status

# Moods (lighting scenes)
homeyctl moods list                    # List all moods
homeyctl moods get <name-or-id>        # Get mood details
homeyctl moods set <name-or-id>        # Activate a mood
homeyctl moods create <file>           # Create mood
homeyctl moods update <name> <file>    # Update mood
homeyctl moods delete <name-or-id>     # Delete mood
```

### Weather
```bash
homeyctl weather current               # Get current weather
homeyctl weather forecast              # Get hourly forecast
```

### Energy
```bash
homeyctl energy live                   # Live power usage
homeyctl energy report day             # Today's energy report
homeyctl energy report week            # This week's report
homeyctl energy report month --date 2025-12  # December report
homeyctl energy report year --date 2025      # Yearly report
homeyctl energy price                  # Show dynamic electricity prices
homeyctl energy price set 0.50         # Set fixed price (e.g., Norgespris)
homeyctl energy price type             # Show current price type
homeyctl energy price type fixed       # Switch to fixed pricing
```

### Insights
```bash
homeyctl insights list                 # List all insight logs
homeyctl insights get <log-id>         # Get historical data
homeyctl insights delete <log-id>      # Delete insight log
```

### Apps & System
```bash
# Apps
homeyctl apps list                     # List installed apps
homeyctl apps get <app-id>             # Get app details
homeyctl apps install <app-id>         # Install app
homeyctl apps uninstall <app-id>       # Uninstall app
homeyctl apps enable <app-id>          # Enable app
homeyctl apps disable <app-id>         # Disable app
homeyctl apps restart <app-id>         # Restart app
homeyctl apps settings get <app-id>    # Get app settings
homeyctl apps settings set <app-id> <setting> <value>  # Set app setting

# System
homeyctl system info                   # System information
homeyctl system name                   # Get system name
homeyctl system name set <name>        # Set system name
homeyctl system reboot                 # Reboot Homey
```

### Dashboards & Notifications
```bash
# Dashboards
homeyctl dashboards list               # List dashboards
homeyctl dashboards get <name-or-id>   # Get dashboard details
homeyctl dashboards create <file>      # Create dashboard
homeyctl dashboards update <name> <file>  # Update dashboard
homeyctl dashboards delete <name-or-id>   # Delete dashboard

# Notifications
homeyctl notifications list            # List notifications
homeyctl notifications send <message>  # Send notification
homeyctl notifications delete <id>     # Delete notification
```

### Snapshot
```bash
homeyctl snapshot                      # Get complete system state
homeyctl snapshot --include-flows      # Include flows in snapshot
```

## Flow JSON Format

### Simple Flow Example
```json
{
  "name": "Turn on lights when arriving",
  "trigger": {
    "id": "homey:manager:presence:user_enter",
    "args": { "user": "user-uuid-here" }
  },
  "conditions": [
    {
      "id": "homey:manager:logic:lt",
      "args": { "value": 20 },
      "droptoken": "homey:device:<device-id>|measure_temperature"
    }
  ],
  "actions": [
    {
      "id": "homey:device:<device-id>:thermostat_mode_heat",
      "args": { "mode": "heat" }
    }
  ]
}
```

## Critical: Droptoken Format

When referencing device capabilities in conditions, use pipe (|) separator:
```
CORRECT: "homey:device:abc123|measure_temperature"
WRONG:   "homey:device:abc123:measure_temperature"
```

## ID Format Reference

| Type | Format | Example |
|------|--------|---------|
| Device action | homey:device:<id>:<capability> | homey:device:abc123:on |
| Manager trigger | homey:manager:<manager>:<event> | homey:manager:presence:user_enter |
| Logic condition | homey:manager:logic:<operator> | homey:manager:logic:lt |
| Droptoken | homey:device:<id>\|<capability> | homey:device:abc123\|measure_temperature |

## Common Triggers
- homey:manager:presence:user_enter - User arrives home
- homey:manager:presence:user_leave - User leaves home
- homey:manager:time:time - At specific time
- homey:device:<id>:<capability>_changed - Device state changes

## Common Conditions
- homey:manager:logic:lt - Less than (use with droptoken)
- homey:manager:logic:gt - Greater than (use with droptoken)
- homey:manager:logic:eq - Equals (use with droptoken)

## Flow Update Behavior

`homeyctl flows update` does a **partial/merge update**:
- Only fields you include will be changed
- Omitted fields keep their existing values
- To remove conditions/actions, explicitly set empty array: `"conditions": []`

```bash
# Rename a flow
echo '{"name": "New Name"}' > rename.json
homeyctl flows update "Old Name" rename.json

# Remove all conditions from a flow
echo '{"conditions": []}' > clear.json
homeyctl flows update "My Flow" clear.json
```

## Output Format

All list commands return flat JSON arrays for easy parsing:
```bash
# Find flow by name
homeyctl flows list | jq '.[] | select(.name | test("pult";"i"))'

# Get all enabled flows
homeyctl flows list | jq '.[] | select(.enabled)'

# Get device IDs by name
homeyctl devices list | jq '.[] | select(.name | test("office";"i")) | .id'
```

## Workflow Tips

1. **Use snapshot for context**: Run `homeyctl snapshot` to get complete system overview in one call
2. **Filter lists**: Use `--filter "name"` on list commands to quickly find items
3. **Device shortcuts**: Use `homeyctl devices on/off <device>` instead of setting `onoff` capability
4. **Get device IDs first**: Run `homeyctl devices list` to find device IDs
5. **Get user IDs**: Run `homeyctl users list` for presence triggers
6. **Check capabilities**: Run `homeyctl devices get <id>` to see available capabilities
7. **Validate before creating**: The CLI validates flow JSON and warns about common mistakes
8. **Test flows**: Use `homeyctl flows trigger "Flow Name"` to test manually

## Connection Modes

homeyctl supports three connection modes:

```bash
homeyctl config set-connection-mode auto    # Auto-detect best connection
homeyctl config set-connection-mode local   # Force local network connection
homeyctl config set-connection-mode cloud   # Force cloud connection
```

The default `auto` mode will use local network when available, falling back to cloud.

## Snapshot Command for AI Context

The `snapshot` command provides a complete overview of your Homey system in a single call, making it ideal for AI assistants to understand your setup:

```bash
homeyctl snapshot                    # Devices, zones, users, weather, energy
homeyctl snapshot --include-flows    # Add flows to snapshot
```

This returns all essential data: devices with capabilities, zones, users, current weather, energy usage, and optionally flows.
