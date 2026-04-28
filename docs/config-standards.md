# Game Server Config Standards

This document defines the standard format for all Source/GoldSrc engine server
configuration files in this repository. All new configs and updates to existing
configs must follow these standards.

---

## File Format

- **Line endings**: CRLF (`\r\n`)
- **Encoding**: UTF-8 (ASCII subset in practice)
- **Filename**: `server.cfg` for all Source/GoldSrc engine games
- **Trailing newline**: One blank line at the end of the file

---

## Header

Every config file must begin with an 80-character header block:

```
// ****************************************************************************
// [Game Name]
// Config - server.cfg
// Date - DD/MM/YYYY
// ****************************************************************************
```

- The asterisk lines are exactly 80 characters
- `[Game Name]` is the full display name of the game or mod (e.g. `Counter-Strike`)
- `Date` reflects the date the file was last significantly updated

---

## Section Dividers

Sections are separated by an 80-character dot-padded divider:

```
// .................................. Basic ................................. //
```

The section name is centred between dot padding. Sections with an odd total
inner width will have one extra dot on the left side — this is consistent and
acceptable (e.g. `Security` produces an 81-character line).

**Standard section dividers** (copy-paste ready):

```
// .................................. Basic ................................. //
// ................................. Security ................................ //
// ............................. Server Logging ............................. //
// .............................. Communication ............................. //
// ................................ Gameplay ................................ //
// .............................. Map Rotation .............................. //
// ............................. Fast Download .............................. //
// ................................ Ban List ................................ //
// ............................ Startup Commands ............................ //
```

For game-specific sections, generate the divider so it totals 80 characters
(or 81 for odd-length names).

---

## Cvar Comment Format

Every cvar must have a single-line comment on the line immediately above it:

```
// cvarname - Description.
cvarname value
```

- **One line only** — no separate `Default:` or `Recommended:` sub-lines
- The value set in the config IS the recommendation
- Description is in sentence case and ends with a period
- Source descriptions from [Valve-Server-cvarlist](https://github.com/dgibbs64/Valve-Server-cvarlist)
  — files are named `[shortname]-cvarlist.txt`
- For mod-specific cvars not in the cvarlist, write a clear description

**Exec/command format:**

```
// exec file.cfg - Description.
exec file.cfg
```

**Blank line between each cvar block** (comment + value pair):

```
// hostname - Name of the server.
hostname "SERVERNAME"

// rcon_password - Remote console password.
rcon_password "ADMINPASSWORD"
```

---

## Standard Sections

Sections must appear in the following order. Sections marked *(optional)* may
be omitted if the game does not use those features.

### 1. Basic *(required)*

Server identity and access credentials. Always the first section.

| Cvar | Notes |
|------|-------|
| `hostname` | Name of the server |
| `rcon_password` | Remote console password |
| `sv_password` | Server password for private servers |
| `sv_contact` | Contact email for server admin |
| `maxplayers` | Maximum number of players |

### 2. Security *(required)*

Server security and integrity settings.

| Cvar | Notes |
|------|-------|
| `sv_lan` | LAN mode — disables Steam authentication |
| `sv_cheats` | Allow cheat commands |
| `sv_pure` | Enforce pure server file checking (Source only) |
| `sv_clienttrace` | Bullet trace accuracy (GoldSrc only) |
| `pausable` | Allow server to be paused (GoldSrc only) |
| `sv_rcon_*` | RCON security tuning (if included) |

### 3. Server Logging *(required)*

| Cvar | Notes |
|------|-------|
| `log` | Enable server log output |
| `sv_logbans` | Log bans in the server logs |
| `sv_logecho` | Echo log information to the console |
| `sv_logfile` | Log server information to file |
| `sv_log_onefile` | Log to only one file |

### 4. Communication *(required)*

| Cvar | Notes |
|------|-------|
| `sv_voiceenable` | Enable voice communications |
| `sv_alltalk` | Allow all players to hear each other regardless of team |
| `sv_voicecodec` | Voice codec to use (Source only) |

### 5. Gameplay *(required)*

Standard engine cvars controlling game rules, physics, and mechanics. Use this
section for cvars that exist across multiple Source/GoldSrc games — not for
cvars unique to one specific game (those go in **[Game Name] Settings**).

Includes:

- `mp_*` cvars (timelimit, friendlyfire, teamplay, roundtime, etc.)
- `sv_maxspeed`, `sv_gravity`, `sv_friction`, `sv_airaccelerate`
- `sv_minrate`, `sv_maxrate`, `sv_mincmdrate`, `sv_maxcmdrate`,
  `sv_minupdaterate`, `sv_maxupdaterate` (network bandwidth)
- `sv_idlekick`, `sv_pure`, `sv_cheats` if not already in Security
- GoldSrc: `deathmatch`, `decalfrequency`, `sys_ticrate`, `hpk_maxsize`
- `sv_aim` (GoldSrc autoaim)

### 6. Map Rotation *(optional)*

Include only if the game supports map cycling.

| Cvar | Notes |
|------|-------|
| `map` | Default starting map |
| `mapcyclefile` | Map cycle file |

### 7. Fast Download *(optional)*

Include only if the server provides a fast download endpoint.

| Cvar | Notes |
|------|-------|
| `sv_allowdownload` | Allow clients to download files from the server |
| `sv_allowupload` | Allow clients to upload customisation files |
| `sv_downloadurl` | URL of the fast download server |

### 8. Ban List *(required)*

**Source engine:**
```
// exec banned_user.cfg - Load the banned user ID list.
exec banned_user.cfg

// exec banned_ip.cfg - Load the banned IP address list.
exec banned_ip.cfg

// writeid - Write banned user IDs to banned_user.cfg.
writeid

// writeip - Write banned IPs to banned_ip.cfg.
writeip
```

**GoldSrc engine:**
```
// exec listip.cfg - Load the banned IP address list.
exec listip.cfg

// exec banned.cfg - Load the banned user list.
exec banned.cfg
```

### 9. Startup Commands *(optional)*

One-time commands executed at server start. Examples: `exec motd.txt`,
custom plugin commands.

### 10. [Game Name] Settings *(optional)*

Game-specific cvars that are **unique to that game or mod** and do not exist in
any other Source/GoldSrc game. If a cvar begins with a game-specific prefix
(e.g. `sbox_`, `jb_`, `zm_`, `sf_`, `ins_`) or is otherwise not shared with
other games, it belongs here.

**Standard engine cvars (`mp_*`, `sv_*`, etc.) must never go in this section**
— they always belong in the appropriate standard section above (Security,
Communication, Gameplay, etc.).

The section name must match the full display name of the game or mod followed
by ` Settings`:

- `Garry's Mod Settings` — `sbox_*` sandbox cvars
- `Insurgency Settings` — `sv_vote_*`, `mp_coop_*` vote and coop system
- `Fistful of Frags Settings` — `fof_*` game-specific cvars
- `Zombie Master: Reborn Settings` — `zm_*` game-specific cvars

If a game has no game-specific cvars, omit this section entirely.

---

## Section Name Standards

Use exactly the names below. Do not invent alternatives.

| Correct name | Do not use |
|---|---|
| `Basic` | `Server Info`, `Server Settings`, `Settings` |
| `Security` | `Server Security`, `Admin Ops`, `RCON Configuration`, `Server Query` |
| `Server Logging` | `Logging`, `Log Settings`, `Logs` |
| `Communication` | `Voice`, `Voice Settings`, `Comms` |
| `Gameplay` | `Runtime`, `In Game`, `Misc`, `Sandbox Rules`, `Time Limits/Round Settings`, `Coop Mode Settings` |
| `Map Rotation` | `Map Cycle`, `Map Cycles`, `Mapcycle` |
| `Fast Download` | `Download`, `Downloads`, `Fast DL` |
| `Ban List` | `Bans`, `Ban Files`, `Banlists` |
| `Startup Commands` | `Game Specific Commands`, `Init Commands`, `Startup` |

---

## Template Variables

All configs must use the following placeholder values. These are replaced by
server operators with real values:

| Placeholder | Replaces |
|---|---|
| `SERVERNAME` | Server hostname |
| `ADMINPASSWORD` | RCON password |
| `ADMINPASSWORD` | sv_password (if included) |
| `""` (empty string) | sv_contact (leave blank — operators fill in) |

---

## Cvar Description Source

Use [https://github.com/dgibbs64/Valve-Server-cvarlist](https://github.com/dgibbs64/Valve-Server-cvarlist)
for authoritative cvar descriptions. Files are named `[shortname]-cvarlist.txt`
and accessible at:

```
https://raw.githubusercontent.com/dgibbs64/Valve-Server-cvarlist/main/[shortname]-cvarlist.txt
```

The shortname matches the directory name in this repository (e.g. `css` for
`css/server.cfg`).

For cvars not present in the cvarlist (mod-specific cvars), write a concise
description in sentence case ending with a period.
