# KapyHolograms — Documentation

> **Version:** 1.0 · **Server:** Paper 1.19.4+ · **Java:** 21+

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Requirements & Installation](#2-requirements--installation)
3. [Commands Reference](#3-commands-reference)
4. [Permissions](#4-permissions)
5. [config.yml — Full Reference](#5-configyml--full-reference)
6. [Text Formatting: MiniMessage vs Legacy](#6-text-formatting-minimessage-vs-legacy)
7. [Line Types](#7-line-types)
8. [Hologram Types](#8-hologram-types)
9. [Click Actions](#9-click-actions)
10. [Hologram File Format](#10-hologram-file-format)
11. [PlaceholderAPI Integration](#11-placeholderapi-integration)
12. [GUI Edit Menu](#12-gui-edit-menu)
13. [Localization (i18n)](#13-localization-i18n)
14. [Developer API](#14-developer-api)

---

## 1. Introduction

**KapyHolograms** is a next-generation hologram plugin for Paper servers. It renders holograms using Minecraft's native `TEXT_DISPLAY` entities (introduced in 1.20.5), which means no Armor Stand tricks — just clean, modern rendering.

### Key Features

| Category | Feature |
|---|---|
| 🖥️ Rendering | Native `TEXT_DISPLAY` entities — supports text, items, item heads |
| 👁️ FOV Culling | Holograms are shown only when a player is actually facing them |
| 📍 Spatial Index | Chunk-based O(1) lookup — scales to thousands of holograms |
| ⚡ Event-driven Visibility | Show/hide is triggered by movement events, not a global tick |
| 📏 LOD Updates | Distant holograms update less often to save CPU |
| 📄 Per-file Storage | Each hologram is its own `.yml` file under `holograms/` |
| 🖱️ Clickable Lines | Six action types: command, console command, message, URL, teleport, page switch |
| 🎭 Multi-page | Multiple pages per hologram with automatic or manual switching |
| 🎨 Rich Text | MiniMessage (gradients, hex colors) **or** Legacy (`&`-codes) — your choice |
| 🔧 PlaceholderAPI | Per-player placeholder support with LOD-aware refresh |
| 🌐 Localization | Built-in English, Russian, and Ukrainian translations |
| 📦 Clean API | Fluent builder API for developers |

---

## 2. Requirements & Installation

### Requirements

- **Minecraft server:** Paper 1.20.5 or newer (Folia not tested)
- **Java:** 21 or newer
- **Optional:** [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) for `%placeholder%` support in hologram text

### Installation

1. Download `KapyHolograms.jar` from the [Releases](../../releases) page.
2. Place the file in your server's `plugins/` folder.
3. Start (or restart) the server — the plugin generates its default files.
4. Edit `plugins/KapyHolograms/config.yml` as needed, then run `/kh reload`.

### Upgrading

Simply replace the old `.jar` with the new one and restart. Hologram data files under `holograms/` are preserved between versions.

---

## 3. Commands Reference

All commands use the root command `/kapyholograms` (alias: `/kh`).

> **Tip:** Tab-completion is supported for hologram names and numeric indices.

### 3.1 Hologram Management

| Command | Description | Permission |
|---|---|---|
| `/kh create <name>` | Create a hologram at your current position | `kapyholograms.command.create` |
| `/kh delete <name>` | Permanently delete a hologram and its file | `kapyholograms.command.delete` |
| `/kh list [page]` | List all holograms (paginated, 10 per page) | `kapyholograms.command.list` |
| `/kh info <name>` | Show details: location, pages, lines, render distance | `kapyholograms.command.info` |
| `/kh teleport <name>` | Teleport to a hologram's location | `kapyholograms.command.teleport` |
| `/kh movehere <name>` | Move a hologram to your current position | `kapyholograms.command.movehere` |
| `/kh nearest` | Find and display the closest hologram to you | `kapyholograms.command.nearest` |
| `/kh edit <name>` | Open the interactive GUI edit menu | `kapyholograms.command.edit` |

### 3.2 Line Management

Lines are 1-indexed. The first line added has index `1`.

| Command | Description |
|---|---|
| `/kh line add <holo> <text>` | Append a new line to page 1 of the hologram |
| `/kh line insert <holo> <index> <text>` | Insert a line **before** the given index |
| `/kh line set <holo> <index> <text>` | Replace the line at the given index |
| `/kh line remove <holo> <index>` | Delete the line at the given index |

**All line commands require** `kapyholograms.command.line`.

**Examples:**
```
/kh line add welcome <gradient:#ff5555:#ffaa00>Welcome!</gradient>
/kh line add welcome <gray>Players online: <white>%server_online%
/kh line insert welcome 1 <gold>>>> Server Welcome <<<
/kh line set welcome 2 <gray>Online: <aqua>%server_online%
/kh line remove welcome 3
```

### 3.3 Page Management

Pages are 1-indexed. Every hologram starts with one page.

| Command | Description |
|---|---|
| `/kh page add <holo>` | Add a blank page to the hologram |
| `/kh page remove <holo> <index>` | Remove the page at the given index (cannot remove the last page) |
| `/kh page switch <holo> <index>` | Switch the hologram to display the given page for all viewers |

**All page commands require** `kapyholograms.command.page`.

### 3.4 Utility

| Command | Description | Permission |
|---|---|---|
| `/kh reload` | Reload `config.yml`, all message files, and all hologram data files | `kapyholograms.reload` |
| `/kh help [page]` | Display the paginated help menu | *(none required)* |

---

## 4. Permissions

| Permission | Description | Default |
|---|---|---|
| `kapyholograms.admin` | Grants all KapyHolograms permissions | `op` |
| `kapyholograms.use` | Allows interacting with clickable holograms | `true` (everyone) |
| `kapyholograms.reload` | Reload the plugin config and holograms | `op` |
| `kapyholograms.command.create` | Create holograms | `op` |
| `kapyholograms.command.delete` | Delete holograms | `op` |
| `kapyholograms.command.edit` | Open the GUI edit menu | `op` |
| `kapyholograms.command.list` | List all holograms | `op` |
| `kapyholograms.command.info` | View hologram info | `op` |
| `kapyholograms.command.teleport` | Teleport to holograms | `op` |
| `kapyholograms.command.movehere` | Move holograms to player location | `op` |
| `kapyholograms.command.nearest` | Find the nearest hologram | `op` |
| `kapyholograms.command.line` | Add, edit, remove lines | `op` |
| `kapyholograms.command.page` | Add, remove, switch pages | `op` |

`kapyholograms.admin` is a parent permission — granting it automatically grants all child permissions listed above.

---

## 5. config.yml — Full Reference

The main configuration file is located at `plugins/KapyHolograms/config.yml`.

```yaml
# ─────────────────────────────────────────────
#  KapyHolograms — Main Configuration
# ─────────────────────────────────────────────

# Language for plugin messages sent to players.
# Available: en (English), ru (Russian), uk (Ukrainian)
language: en

# Text format used for hologram LINE CONTENT only.
# This does NOT affect plugin messages (they always use MiniMessage).
#
# minimessage — modern Adventure format:
#   <gold>Text</gold>  <gradient:#ff0000:#00ff00>Text</gradient>  <#ff5500>Text</#ff5500>
#
# legacy — classic Bukkit/Essentials format:
#   &6Gold  &lBold  &#ff5500Hex  &r Reset
text-format: minimessage

# ─── Performance ──────────────────────────────
performance:

  # Only send holograms to players who are actually facing them.
  # Greatly reduces the number of packets sent in areas with many holograms.
  fov-culling: true

  # The half-angle of the field-of-view cone used for culling (degrees).
  # 60 = strict (only show when looking almost directly at the hologram)
  # 90 = standard (show within a 180° front arc)
  # 120 = relaxed (show even when looking slightly away)
  fov-angle: 120.0

  # Default render distance for holograms (blocks).
  # Individual holograms can override this via the GUI or hologram file.
  default-render-distance: 48.0

  # Level-of-Detail (LOD) — updates holograms at different rates based on distance.
  lod:
    enabled: true

    # Holograms closer than near-distance update every near-update-ticks ticks.
    near-distance: 10.0
    # Holograms closer than mid-distance (but beyond near) update every mid-update-ticks ticks.
    mid-distance: 30.0
    # Holograms beyond mid-distance update every far-update-ticks ticks.

    near-update-ticks: 2    # ~0.1 s at 20 TPS
    mid-update-ticks: 20    # ~1 s  at 20 TPS
    far-update-ticks: 60    # ~3 s  at 20 TPS

  # How many ticks between consecutive visibility recalculations for a single player.
  # Increase to reduce CPU usage; decrease for more responsive show/hide.
  visibility-throttle-ticks: 2

# ─── Hologram Defaults ────────────────────────
hologram-defaults:

  # Default billboard mode for new holograms.
  # CENTER     — always faces the player (standard)
  # HORIZONTAL — stays upright but rotates horizontally only
  # VERTICAL   — can tilt toward player vertically but not horizontally
  # FIXED      — does not rotate; uses fixed-yaw angle
  billboard: CENTER

  # Whether hologram text has a drop shadow.
  text-shadow: true

  # Default background transparency (0 = fully transparent, 255 = fully opaque black).
  background-color: 0

  # Vertical spacing between lines (in blocks).
  line-spacing: 0.3

# ─── Storage ──────────────────────────────────
storage:
  # Subfolder under plugins/KapyHolograms/ where hologram files are stored.
  holograms-folder: holograms

# ─── PlaceholderAPI ───────────────────────────
placeholders:
  # Set to false to disable PlaceholderAPI even if it is installed.
  enabled: true
```

### Quick-Reference: Important Settings

| Setting | Description | Default |
|---|---|---|
| `language` | Interface language (`en`, `ru`, `uk`) | `en` |
| `text-format` | Line text parser (`minimessage` or `legacy`) | `minimessage` |
| `performance.fov-culling` | Enable FOV-based hologram culling | `true` |
| `performance.fov-angle` | FOV cone half-angle in degrees | `120.0` |
| `performance.default-render-distance` | View range for holograms (blocks) | `48.0` |
| `performance.lod.enabled` | Enable LOD update rate scaling | `true` |
| `hologram-defaults.line-spacing` | Distance between lines (blocks) | `0.3` |
| `hologram-defaults.text-shadow` | Text drop shadow | `true` |
| `hologram-defaults.background-color` | Background alpha (0–255) | `0` |

---

## 6. Text Formatting: MiniMessage vs Legacy

KapyHolograms supports **two text formats** for hologram line content. Choose one via `text-format` in `config.yml`.

> **Note:** Plugin messages (commands, chat, GUI tooltips) always use MiniMessage regardless of this setting.

### 6.1 MiniMessage (default, recommended)

MiniMessage is Adventure's modern markup language. It supports gradients, hex colours, hover/click events, and more.

| Tag | Result |
|---|---|
| `<red>text</red>` | Red text |
| `<gold>text` | Gold text (auto-closes) |
| `<#ff5500>text` | Hex color (shorthand) |
| `<color:#ff5500>text</color>` | Hex color (explicit) |
| `<bold>text</bold>` | Bold |
| `<italic>`, `<underlined>`, `<strikethrough>`, `<obfuscated>` | Decorations |
| `<gradient:#ff0000:#0000ff>text</gradient>` | Color gradient |
| `<rainbow>text</rainbow>` | Rainbow gradient |
| `<reset>` | Reset all formatting |

**Examples:**
```
<gradient:#ff5555:#ffaa00><bold>Welcome!</bold></gradient>
<gray>Players online: <white>%server_online%
<#55ff55>✔ <white>VIP Server
```

Full MiniMessage reference: https://docs.advntr.dev/minimessage/format.html

### 6.2 Legacy (`text-format: legacy`)

Legacy format uses `&` as the formatting character (compatible with Essentials, CMI, etc.).

| Code | Result |
|---|---|
| `&0` – `&9`, `&a` – `&f` | Standard 16 Minecraft colors |
| `&l` | Bold |
| `&o` | Italic |
| `&n` | Underline |
| `&m` | Strikethrough |
| `&k` | Obfuscated |
| `&r` | Reset |
| `&#RRGGBB` | Hex color (Adventure extension) |

**Examples:**
```
&#ff5500&lWelcome!&r
&7Players online: &f%server_online%
&a✔ &fVIP Server
```

---

## 7. Line Types

Each line in a hologram has a type that determines how it is rendered.

| Type | How to create | Description |
|---|---|---|
| `TEXT` | `/kh line add <holo> <text>` | A text line parsed according to `text-format` |
| `ITEM` | Set `type: ITEM` in the hologram file | Displays a floating item (e.g. `DIAMOND_SWORD`) |
| `HEAD` | Set `type: HEAD` in the hologram file | Displays a floating player skull or custom head |
| `EMPTY` | Set `type: EMPTY` in the hologram file | An invisible spacer line |

> Currently, `ITEM`, `HEAD`, and `EMPTY` lines can only be added directly to the hologram's YAML file. The command `/kh line add` always creates `TEXT` lines.

### Dynamic Lines

A text line is considered **dynamic** (updated per-player) if its content contains a `%` character. Dynamic lines are re-evaluated for each viewer using PlaceholderAPI (if available). Non-dynamic lines are computed once and reused, which is more efficient.

---

## 8. Hologram Types

The **hologram type** controls how the display entity is oriented in the world.

| Type | Description | Best for |
|---|---|---|
| `VERTICAL` | The hologram always faces the player (billboard mode: `CENTER`) | Default floating text |
| `FIXED` | The hologram faces a fixed compass direction defined by `fixed-yaw` | Signs, walls, directional displays |

### FIXED Yaw Reference

| `fixed-yaw` | Direction the hologram faces |
|---|---|
| `0` | South (+Z) |
| `90` | West (−X) |
| `180` | North (−Z) |
| `270` | East (+X) |

### Double-Sided (FIXED only)

When `double-sided: true`, a mirror copy of the hologram is spawned on the opposite side, so the text is readable from both the front and back. Only applicable when `type: FIXED`.

---

## 9. Click Actions

Lines can have **click actions** that execute when a player right-clicks a hologram. Multiple actions can be attached to a single line and are all executed in order.

> Click events require the player to have the `kapyholograms.use` permission (default: `true` for everyone).

### Action Types

| Type | `value` format | Description |
|---|---|---|
| `RUN_COMMAND` | `say Hello` | Executes a command as the clicking player |
| `CONSOLE_COMMAND` | `op Steve` | Executes a command via the console |
| `SEND_MESSAGE` | `<green>Hello!` | Sends a MiniMessage-formatted message to the player |
| `OPEN_URL` | `https://example.com` | Sends a clickable URL message to the player |
| `TELEPORT` | `world,100,64,200,0,0` | Teleports the player (`world,x,y,z[,yaw,pitch]`) |
| `SWITCH_PAGE` | `2` | Switches the hologram to the specified page for the player |
| `NONE` | *(any)* | No-op; used as a placeholder |

### Adding Actions via YAML

Actions can only be added by editing the hologram file directly (no command exists yet):

```yaml
# plugins/KapyHolograms/holograms/shop.yml
pages:
  - lines:
      - type: TEXT
        content: "<gold><bold>Click to open shop!"
        actions:
          - type: RUN_COMMAND
            value: shop
          - type: SEND_MESSAGE
            value: "<green>Opening shop..."
```

---

## 10. Hologram File Format

Each hologram is stored as a separate YAML file in `plugins/KapyHolograms/holograms/`.

### Full Example

```yaml
# plugins/KapyHolograms/holograms/welcome.yml

name: welcome
world: world
x: 0.5
y: 64.0
z: 0.5

# Individual render distance override (-1 = use config default)
render-distance: -1.0

enabled: true

# VERTICAL or FIXED
type: VERTICAL

# CENTER, LEFT, or RIGHT
text-alignment: CENTER

# Background transparency (0 = transparent, 255 = opaque)
background-alpha: 0

# Content update interval in seconds (0 = use LOD system from config)
update-interval: 0

# Auto page-switch interval in seconds (0 = disabled)
page-interval: 5

# Used only when type: FIXED. Compass direction in degrees.
# 0=S  90=W  180=N  270=E
fixed-yaw: 0.0

# Mirror the hologram on the back side (type: FIXED only)
double-sided: false

pages:
  - lines:
      - type: TEXT
        content: "<gradient:#ff5555:#ffaa00><bold>Welcome!</bold></gradient>"
      - type: TEXT
        content: "<gray>Players online: <white>%server_online%"
        actions:
          - type: RUN_COMMAND
            value: list
      - type: EMPTY
      - type: ITEM
        content: DIAMOND
  - lines:
      - type: TEXT
        content: "<gold>Page 2 — More info"
      - type: TEXT
        content: "<gray>Server IP: <white>play.example.com"
```

### Field Reference

| Field | Type | Description |
|---|---|---|
| `name` | string | Unique hologram identifier (must match the filename without `.yml`) |
| `world` | string | World name |
| `x`, `y`, `z` | double | Hologram position |
| `render-distance` | double | Override render distance in blocks (`-1` = use config) |
| `enabled` | boolean | Whether the hologram is visible |
| `type` | enum | `VERTICAL` or `FIXED` |
| `text-alignment` | enum | `CENTER`, `LEFT`, or `RIGHT` |
| `background-alpha` | int | Background opacity `0`–`255` |
| `update-interval` | int | Content refresh period in seconds (`0` = LOD-driven) |
| `page-interval` | int | Seconds between automatic page switches (`0` = disabled) |
| `fixed-yaw` | float | Compass yaw for FIXED type (degrees) |
| `double-sided` | boolean | Mirror copy on the back (FIXED only) |
| `pages` | list | List of page objects |
| `pages[].lines` | list | List of line objects on this page |
| `pages[].lines[].type` | enum | `TEXT`, `ITEM`, `HEAD`, or `EMPTY` |
| `pages[].lines[].content` | string | Text string or material name |
| `pages[].lines[].actions` | list | Optional click actions |
| `pages[].lines[].actions[].type` | enum | Action type (see §9) |
| `pages[].lines[].actions[].value` | string | Action parameter (see §9) |

---

## 11. PlaceholderAPI Integration

KapyHolograms integrates with [PlaceholderAPI](https://www.spigotmc.org/resources/placeholderapi.6245/) to show per-player values inside hologram lines.

### Setup

1. Install PlaceholderAPI on your server.
2. Install any PAPI expansion you need (e.g. `Player`, `Server`, `Vault`, etc.).
3. Make sure `placeholders.enabled: true` in `config.yml`.
4. Use `%placeholder%` syntax in your hologram text lines.

### How It Works

- A line containing `%` is marked as **dynamic**.
- Dynamic lines are re-evaluated individually for each player who views the hologram.
- The LOD system controls how frequently dynamic lines are refreshed based on distance.
- Non-dynamic lines are rendered once and shared for all players — more efficient.

### Examples

```
<gray>Welcome, <gold>%player_name%<gray>!
<gray>Your ping: <white>%player_ping% ms
<gray>Online: <white>%server_online%/<gray>%server_max%
<gray>Your balance: <gold>$%vault_eco_balance%
```

---

## 12. GUI Edit Menu

The GUI edit menu provides a convenient in-game interface for tweaking all hologram properties without editing YAML files.

### Opening the Menu

```
/kh edit <name>
```

Requires `kapyholograms.command.edit` (default: `op`).

### Menu Layout

```
┌───────────────────────────────────────────────────────┐
│  [Pane] [Pane] [Pane] [Pane] [Pane] [Pane] [Pane]... │  ← Row 0 (header)
│  [Pane] [Ena]  [Type] [Aln]  [Pane] [Bg]   [Pane]    │  ← Row 1 (basic settings)
│         [Move]                                         │
│  [Pane] [Upd] [Page] [Pane] [Dist] [Pane] [Pane]...  │  ← Row 2 (intervals)
│  [Pane] [Yaw] [DblS] [Pane] ....                      │  ← Row 3 (FIXED settings)
│  [Pane] ... [CLOSE] ...                                │  ← Row 5 (bottom)
└───────────────────────────────────────────────────────┘
```

### Button Reference

| Slot | Item | LMB | RMB | Q (Drop) |
|---|---|---|---|---|
| 10 | **Enable/Disable** | Toggle on/off | — | — |
| 11 | **Type** (VERTICAL/FIXED) | Cycle type | — | — |
| 12 | **Text Alignment** | Next (CENTER→LEFT→RIGHT) | Previous | — |
| 14 | **Background Opacity** | +5 | −5 | Reset to 0 |
| 16 | **Move Here** | Move hologram to your location | — | — |
| 19 | **Update Interval** | +1 sec | −1 sec | Reset to 0 (LOD) |
| 20 | **Page Cycle Interval** | +1 sec | −1 sec | Reset to 0 (off) |
| 22 | **Render Distance** | +5 blocks | −5 blocks | Reset to default |
| 28 | **Fixed Yaw** *(FIXED only)* | +5° | −5° | Reset to 0° |
| 29 | **Double-Sided** *(FIXED only)* | Toggle | — | — |
| 49 | **Close** | Close inventory | — | — |

All changes are saved to the hologram's YAML file immediately.

---

## 13. Localization (i18n)

All player-facing messages and GUI strings are stored in language files under `plugins/KapyHolograms/`.

### Available Languages

| Code | Language | File |
|---|---|---|
| `en` | English | `messages_en.yml` |
| `ru` | Russian (Русский) | `messages_ru.yml` |
| `uk` | Ukrainian (Українська) | `messages_uk.yml` |

### Changing the Language

Edit `config.yml`:
```yaml
language: ru   # or: en, uk
```

Then run `/kh reload`.

### Customizing Messages

Open the corresponding `messages_*.yml` file. All values support MiniMessage formatting tags.

**Message file structure:**

```yaml
# Prefix shown before all plugin messages
prefix: "<dark_gray>[<gradient:#ff5555:#ffaa00>KapyHolograms</gradient>]</dark_gray> "

# Command messages (use <placeholder> syntax)
hologram-created: "<green>Hologram <white><name></white> created."
hologram-not-found: "<red>Hologram '<white><name></white>' not found."
# ... etc.

# GUI Edit Menu — item names and lore
menu-edit-title: "<dark_gray>Hologram: <gold>{name}"
menu-edit-enabled-name: "<white>Hologram: {status}"
menu-edit-enabled-on: "<green>ON"
menu-edit-enabled-off: "<red>OFF"
menu-edit-enabled-lore:
  - "<gray>Toggle hologram visibility"
  - ""
  - "<yellow>LMB <gray>— toggle"
# ... etc.
```

### Adding a Custom Language

1. Copy `messages_en.yml` to `messages_XX.yml` (replace `XX` with your code).
2. Translate the values.
3. Set `language: XX` in `config.yml` and run `/kh reload`.

---

## 14. Developer API

Coming soon

---

*For bug reports and feature requests, open an issue on [GitHub](https://github.com/ImFriendlyy/KapyHolograms/issues).*
