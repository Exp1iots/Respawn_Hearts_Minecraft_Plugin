# Respawn Hearts

A lifesteal plugin for Minecraft, written in [Skript](https://skriptlang.github.io/Skript/).
Kill players to grow your hearts. Die, and your hearts reset to your respawn heart count.

Built and tested on Paper 1.21.11 with Skript 2.16.2 and SkBee 3.26.0.

## How it works

Each player has two stats, counted in hearts:

- **Max health**: the hearts you play with. Kill another player and you gain one heart. The default cap is 20.
- **Respawn hearts**: the hearts you return with after death. By default new players start at 10 with a cap of 15.

> These can be configured in `setup.sk`.

When a player dies:

1. The killer gains one max heart, up to 20.
2. The victim's max health resets to their respawn heart count.

Gains from kills are lost on death. Respawn hearts set the floor you return to.

### Kill cooldown

Each killer and victim pair has a cooldown. The default period is 5 minutes (can be configured in setup.sk).
A kill inside the cooldown grants no heart. The action bar shows the remaining wait.

### Respawn Heart item

The Respawn Heart is a knowledge book with a custom texture. Right-click it to gain one respawn heart, up to 15. Your max health rises to match the new count when it is lower. At the cap the item stays in your hand and a sound plays.

Use `/respawn_heart` to get the item (op only).

## Commands

**Players**

| Command | Description |
|---|---|
| `/health_stats` | Show your max health and respawn hearts. |

**Admins (op)**

| Command | Description |
|---|---|
| `/respawn_heart` | Give yourself a Respawn Heart item. |
| `/set_max_hearts <player> <hearts>` | Set a player's max health. |
| `/set_respawn_hearts <player> <hearts>` | Set a player's respawn hearts. |
| `/health_stats_of <player>` | Show another player's health stats. |
| `/enable_force_respawn <player>` | Send a player to the respawn screen. |

**Debug**

| Command | Description | Op only |
|---|---|---|
| `/current_time` | Print the game time and date. | No |
| `/show_cooldown <killer> <victim>` | Print one cooldown variable. | Yes |
| `/clear_cooldown <killer> <victim>` | Delete one cooldown variable. | Yes |
| `/show_deathCooldownPeriod` | Print the cooldown period. | No |

Note: the kill code can print debug messages to the killer's chat. Set `showDebugMessages` to 1 to enable them.

## Installation

You need a Paper 1.21.11 server with Java 21 or newer.

1. Install Skript 2.16.2 and SkBee 3.26.0 on your server.
2. Copy the four `.sk` files from `MC Server/plugins/Skript/scripts/` into your server's `plugins/Skript/scripts/` folder.
3. Add the cooldowns database to your `plugins/Skript/config.sk`. See the [Databases](#databases) section.
4. Restart the server.

## Databases

Skript saves variables in the databases listed in `plugins/Skript/config.sk`. This plugin uses a dedicated cooldowns database. It stores all `cooldown.*` variables in `cooldowns.csv`, separate from the default `variables.csv`. All other variables, such as `RespawnHearts::*`, go to the default database.

A fresh Skript install has only the default database. Add the block below to the `databases:` list in your `plugins/Skript/config.sk`. Place it above the `default:` database:

```
	cooldowns:
		# This database stores the cooldown variables
		type: CSV
		pattern: cooldown.*
		file: ./plugins/Skript/cooldowns.csv
		backup interval: 2 hours
		backups to keep: -1
```

The default database uses the pattern `.*` and catches every variable that no earlier database matched. The cooldowns database must sit above it, so the `cooldown.*` variables land in `cooldowns.csv`.

Restart the server after you edit config.sk.

## Configuration

Open `MC Server/plugins/Skript/scripts/setup.sk`. Edit the values under `on load`:

| Variable | Default | Meaning |
|---|---|---|
| `maxRespawnHearts` | 15 | Respawn heart cap. |
| `defaultRespawnHearts` | 10 | Respawn hearts for a new player. |
| `maxHearts` | 20 | Max health cap. |
| `deathCooldownPeriod` | 5 minutes | Cooldown for one killer and victim pair. |
| `showDebugMessages` | 0 | Show the kill debug messages (1 = on, 0 = off). |

The script sets these values on every load. Reload the scripts after an edit (`/sk reload all`) to apply them.

## Resource pack

The resource pack gives the Respawn Heart item its heart icon. It replaces the knowledge book texture when the item carries the custom model data `respawn_heart`. The model uses the vanilla HUD heart texture, so texture packs that change the HUD heart also change this item. The pack targets Minecraft 1.21.11.

Install it in one of two ways:

- **Client side**: copy `Resource Packs/Respawn Hearts/Respawn Hearts.zip` into your `resourcepacks` folder. Enable the pack in the game options.
- **Server side**: host the zip at a URL. Set `resource-pack` in your server's `server.properties` to that URL. Players accept the pack on join.

The item works without the pack. It just shows the knowledge book texture.

## Project layout

```
MC Server/plugins/Skript/
  config.sk                   Skript config, includes the cooldowns database.
  scripts/setup.sk            Defaults and per-player setup.
  scripts/player_death.sk     Kill rewards, cooldowns, and death resets.
  scripts/items.sk            Respawn Heart item behavior.
  scripts/commands.sk         All commands.
Resource Packs/Respawn Hearts/   Resource pack source and zip.
```

## Credits

Plugin and resource pack by [Exp1iots](https://github.com/Exp1iots).
Built with [Skript](https://skriptlang.github.io/Skript/) and [SkBee](https://modrinth.com/plugin/skbee).
