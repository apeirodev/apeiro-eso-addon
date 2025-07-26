# Apeiro PvP Addon Specification (apeiro-eso-addon)

**Author:** Apeirogon **Maintainer:** ChatGPT (under instruction of Apeirogon) **Status:** Authoritative Specification **Compliance:** CSA CCM, COBIT **ESO Namespace Prefix:** `Apeiro` **Last Updated:** July 26, 2025 12:13 EDT

---

## Table of Contents

- [1 — Introduction](#1--introduction)
  - [Primary Goals](#primary-goals)
- [2 — Module Specifications](#2--module-specifications)
  - [Module Overview](#module-overview)
  - [2.1 ApeiroCore](#21-apeirocore)
  - [2.2 ApeiroGridSelf](#22-apeirogridself)
  - [2.3 ApeiroGridGroup](#23-apeirogridgroup)
  - [2.4 ApeiroEffects](#24-apeiroeffects)
  - [2.5 ApeiroChat](#25-apeirochat)
  - [2.6 ApeiroLeader](#26-apeiroleader)
  - [2.7 ApeiroFeeds](#27-apeirofeeds)
  - [2.8 ApeiroQoL](#28-apeiroqol)
  - [2.9 ApeiroBurstSync](#29-apeiroburstsync)
  - [2.10 ApeiroQualityOfLife](#210-apeiroqualityoflife)
    - [2.10.1 ApeiroQoLCompass](#2101-apeiroqolcompass)
    - [2.10.2 ApeiroQoLCombatState](#2102-apeiroqolcombatstate)
    - [2.10.3 ApeiroQoLCampStatus](#2103-apeiroqolcampstatus)
    - [2.10.4 ApeiroObjectiveTracker](#2104-apeiroobjectivetracker)
    - [2.10.5 ApeiroIgnoreEnhancer](#2105-apeiroignoreenhancer)
    - [2.10.6 ApeiroAutoResponder](#2106-apeiroautoresponder)
    - [2.10.7 ApeiroLagProfiler](#2107-apeirolagprofiler)
    - [2.10.8 ApeiroAPLog](#2108-apeiroaplog)

## 1 — Introduction

The Apeiro Suite is a high-performance, modular PvP addon and distributed infrastructure designed for **The Elder Scrolls Online (ESO)**. It enhances visibility, coordination, survivability, and intelligence in large-scale PvP scenarios such as **Cyrodiil**, **Imperial City**, and **Battlegrounds**.

### Primary Goals

- Offer the **best-in-class PvP addon** experience with zero assumptions or guesswork
- Fully modular system (e.g., `ApeiroGridSelf`, `ApeiroChat`, `ApeiroEffects`, etc.)
- Distributed client-agent architecture for AI-enhanced filtering, telemetry, and flagging
- Compliance with **CSA Cloud Controls Matrix** and **COBIT** principles
- Discord-based configuration sync, toxic behaviour reporting, and future bot integration
- Complete portability and clarity for any future contributor to understand and maintain
- External configuration and post-session review is provided by the separate **ApeiroClient (Companion App)**, documented independently

---

## 2 — Module Specifications

### Module Overview

| Module Name            | Description                                       | Integration Targets                    |
| ---------------------- | ------------------------------------------------- | -------------------------------------- |
| ApeiroCore             | Core framework, config system, inter-module comms | All modules                            |
| ApeiroGridSelf         | Self unit frame with buffs, shields, procs        | ApeiroEffects                          |
| ApeiroGridGroup        | Group UI with status bars, revive, healer info    | ApeiroEffects, ApeiroRescue, NodeAgent |
| ApeiroEffects          | Buff/debuff/proc tracker                          | GridSelf, GridGroup, EncounterLog      |
| ApeiroChat             | Enhanced chat with moderation + Discord sync      | AutoResponder, IgnoreEnhancer          |
| ApeiroLeader           | Group leader visuals (icon, beam, arrow)          | Grid, QoL                              |
| ApeiroFeeds            | Combat feed and logging UI                        | EncounterLog, ChatClient               |
| ApeiroQoL              | Compass, combat reticle, crown visuals            | Grid modules                           |
| ApeiroBurstSync        | Team burst timing and ult readiness tracking      | ChatClient                             |
| ApeiroObjectiveTracker | Scroll, hammer, and emperor timeline awareness    | Feeds, Server                          |
| ApeiroRescue           | Revive assist beams for downed players            | GridGroup                              |
| ApeiroIgnoreEnhancer   | Tiered ignore system for PvP toxicity             | Chat                                   |
| ApeiroAutoResponder    | Auto replies to flagged players (on hold)         | Chat                                   |
| ApeiroLagProfiler      | Latency debugging and disconnection diagnostics   | Server, GridGroup                      |
| ApeiroAPLog            | Alliance Points tracking and heatmap export       | Feeds                                  |

---

### 2.1 ApeiroCore

Handles module registration, config, inter-module messaging, and UI layout management.

#### Features

- Registers and manages all other Apeiro modules
- Provides global UI wireframe layout mode to drag and reposition UI components
- SavedVariables root: `ApeiroSaved` with per-module subkeys
- Event bus for module communications
- CLI interface: `/apeiro toggle <ModuleName>`, `/apeiro debug`
- LibAddonMenu integration for full GUI configuration
- Exports/imports per-module config sets to disk or server. Provides `/apeiro` CLI and menu interface.

#### Features

- Registers and manages all other Apeiro modules
- SavedVariables root: `ApeiroSaved` with per-module subkeys
- Event bus for module communications
- CLI interface: `/apeiro toggle <ModuleName>`, `/apeiro debug`
- LibAddonMenu integration for full GUI configuration

#### SavedVariables Example:

> Note: Some settings may also be edited externally via the ApeiroClient (Companion App) when ESO is not running.

```lua
ApeiroSaved = {
  ["profile"] = {
    ["Default"] = {
      ["Modules"] = {
        ["ApeiroGridSelf"] = true,
        ["ApeiroChat"] = true
      },
      ["Settings"] = {
        ["LogLevel"] = "debug",
        ["AutoBroadcast"] = true
      }
    }
  }
}
```

#### Dependencies:

- LibAddonMenu-2.0
- LibChatMessage
- LibSavedVars
- LibDebugLogger

#### Behaviour:

- Status panel optional
- Core must never crash even if a module fails

---

### 2.2 ApeiroGridSelf

Individual unit frame (self only) showing current HP, magicka, stamina, shields, procs, debuffs.

#### Features

- Horizontal or vertical bar layout (user-selectable)
- Mirrors configuration and display style of `ApeiroGridGroup` unit frames
- All values toggleable: current, max, %, shields
- Icons: 1 row below, 1 column to right (buffs/debuffs)
- Icons are capped at max width/height = 50% of the height of the unit frame
- Additional marker slots (e.g., priority indicators) available across bottom and right edges
- Each marker can optionally represent a tracked effect:
  - Active: highlighted green
  - Missing: highlighted red
- Auto-size preview with fixed-size default (better for visual memory)
- Fade-out if dead

#### Default Behaviour

- Always show all bars by default
- Dead = greyed-out, empty bars
- Update frequency is user-configurable

#### Performance Control

- Update interval slider in settings
- Option to adapt based on network latency

#### Integration

- Broadcasts status via ApeiroCore
- Receives proximity warnings from Effects module

---

### 2.3 ApeiroGridGroup

Displays the group layout in PvP scenarios, showing up to **12** group members (ESO max) in a grid, each with status, buffs, debuffs, and death visibility.

#### Features

- Default sorting order: **Tank → DPS → Healer** based on ESO party role settings
- Sorting is user-configurable via the addon settings panel
- Fixed horizontal layout grid (3x4 or 4x3 recommended for 12-player groups, customizable rows/cols)
- Mirrors configuration and behaviour of `ApeiroGridSelf` layout
- Marker system enhancements:
  - Each marker may represent a tracked effect
  - Optional countdown timer overlay for that effect
  - Optional bar display extending **rightward** or **downward** from marker
  - Timer bar shows time remaining with configurable colour/opacity
  - Text label shown in or beside bar (e.g., "Major Resolve")
- Individual unit frames display:
  - Health, magicka, stamina, and shield bars
  - Buff and debuff icons
  - Marker slots across bottom and right sides for tracked effects
- Effects tracked via markers:
  - Active = green
  - Missing = red
- Icons follow same size constraint as Self: max 50% of frame height/width
- Dead players fade out with bars empty
- Toggle to hide magicka/stamina bars if full (on by default: always show)
- Configurable update frequency
- Latency-aware update scaling to reduce performance impact
- Optional broadcasting of group health/status to backend via ApeiroCore in PvP scenarios, showing up to **12** group members (ESO max) in a grid, each with status, buffs, debuffs, and death visibility.

#### Features

- Fixed horizontal layout grid (3x4 or 4x3 recommended for 12-player groups, customizable rows/cols)
- Mirrors configuration and behaviour of `ApeiroGridSelf` layout
- Marker system enhancements:
  - Each marker may represent a tracked effect
  - Optional countdown timer overlay for that effect
  - Optional bar display extending **rightward** or **downward** from marker
  - Timer bar shows time remaining with configurable colour/opacity
  - Text label shown in or beside bar (e.g., "Major Resolve")
- Individual unit frames display:
  - Health, magicka, stamina, and shield bars
  - Buff and debuff icons
  - Marker slots across bottom and right sides for tracked effects
- Effects tracked via markers:
  - Active = green
  - Missing = red
- Icons follow same size constraint as Self: max 50% of frame height/width
- Dead players fade out with bars empty
- Toggle to hide magicka/stamina bars if full (on by default: always show)
- Configurable update frequency
- Latency-aware update scaling to reduce performance impact
- Optional broadcasting of group health/status to backend via ApeiroCore

#### Default Behaviour

- Layout is fixed horizontal format for clarity
- All members visible even when dead or disconnected
- Grid fades dead entries and colours them distinctly

#### SavedVariables Example:

```lua
["GridGroup"] = {
  ["Orientation"] = "horizontal",
  ["ShowShield"] = true,
  ["ShowMagicka"] = true,
  ["ShowStamina"] = true,
  ["HideFullMagStam"] = false,
  ["BroadcastGroupStatus"] = true,
  ["Rows"] = 4,
  ["Columns"] = 6,
  ["UpdateInterval"] = 250
}
```

#### Integration

- Mirrors settings from GridSelf where possible
- Pulls buff/debuff events from `ApeiroEffects`
- Optionally sends data to `ApeiroNodeAgent` for telemetry

```lua
-- Audit Notes:
-- ✔ Mirrors Self frame design, consistent UI/UX
-- ✔ Configurable update interval
-- ✔ Icon marker slots and effect tracking logic captured
-- ✔ Dead player behaviour and magicka/stamina toggle confirmed
-- ✔ Broadcast logic tied to ApeiroCore and optionally to NodeAgent
-- ❗ Previously incorrect: ESO group size max is 12, not 24 — layout updated to 3x4 or 4x3
```

---

### 2.4 ApeiroEffects

This module tracks PvP-relevant buffs, debuffs, and proc effects using a server-synchronized ability/effect ID list. It highlights important states visually for Self and Group and contributes to the backend encounter log enrichment.

#### Features

- Server-synced global list of tracked effect and ability IDs
- Detects:
  - Major/Minor Buffs (e.g., Resolve, Brutality, Sorcery)
  - Armor procs and debuffs
  - Heal-over-time and damage-over-time effects
  - Crowd control events
- Optional local scan mode to collect unknown effects and upload for review
- Integrates with `ApeiroGridSelf`, `ApeiroGridGroup`, and `ApeiroEncounterLog`
- Tracked effect display via icon markers in grids
- Colour-coded effect states:
  - Green: Active
  - Red: Missing
  - Grey: Expired/unknown
- List updated automatically post-patch by NodeAgent + server

#### SavedVariables (ApeiroEffectsSaved)

```lua
ApeiroEffectsSaved = {
  ["TrackedEffects"] = {
    ["37096"] = { name = "Major Resolve", category = "Buff" },
    ["39954"] = { name = "Vigor HoT", category = "Healing" },
    -- Server-managed list
  },
  ["LastSyncHash"] = "b4931f8a"
}
```

---

### 2.5 ApeiroChat

This module replaces the default chat with an enhanced interface that includes toxic flagging, auto-responses, tiered mute logic, and a Discord relay integration. All flagged data is optionally synced to the server for moderation review.

#### Features

- Full chat replacement layer (based on pChat model)
- Flagging tiers:
  - Local Only
  - Watched (flagged, tracked, but not hidden)
  - Muted (messages replaced with placeholder)
  - Hard Blocked (auto-ignore)
- Auto-responder (if enabled):
  - Custom message when whispered by flagged user
  - Disabled if user is typing
- Server sync option:
  - Uploads toxic session logs if trusted
  - Does not auto-send unless approved in config
- Discord integration:
  - Role-based log access via bot
  - Webhook forwarding option for trusted mods

#### SavedVariables (ApeiroChatSaved)

```lua
ApeiroChatSaved = {
  ["MuteList"] = {
    ["@ToxicGuy"] = "HardBlocked",
    ["@MaybeSuspicious"] = "Watched"
  },
  ["AutoRespondEnabled"] = true,
  ["ToxicFlags"] = {
    { name = "@ToxicGuy", timestamp = 1720000000, msg = "you suck" },
  }
}
```

---

### 2.6 ApeiroLeader

This module assists group members in locating and responding to the group leader by displaying a configurable overhead icon, visual beam, and a directional arrow similar to RDK. All visual elements are toggleable and colour-customizable.

#### Features

- Leader-only highlight system:
  - Beam pointing from player to leader (blue by default)
  - Icon above leader's head (default: green crown, user-customizable)
  - Directional arrow on HUD indicating leader's direction and range
- Optional out-of-combat toggle (default: always show)
- Customizable icon, arrow style, beam thickness, colours

#### SavedVariables (in ApeiroGridSaved)

```lua
["LeaderAssist"] = {
  ["ShowBeam"] = true,
  ["ShowIcon"] = true,
  ["ShowArrow"] = true,
  ["HideOutOfCombat"] = false,
  ["IconStyle"] = "Crown",
  ["BeamColour"] = { r = 0.3, g = 0.6, b = 1 }
}
```

---

### 2.7 ApeiroFeeds

The ApeiroFeeds module provides real-time event notifications, combat alerts, and log management specifically optimized for PvP scenarios within Elder Scrolls Online (ESO). It enhances situational awareness and response times by consolidating critical combat data and presenting it through a clear and concise user interface.

#### Features and Functionalities:

##### Real-time Event Handling:

- Captures and processes combat events, including incoming and outgoing damage, healing, and status effects.
- Processes notifications for key combat milestones (kills, assists, healing bursts) and special PvP events (resource captures, keep flips, etc.).

##### Configurable Visual and Auditory Alerts:

- Full customization of visual alerts: colours, opacity, size, and position
- User-defined audio cues enhance accessibility

##### Feed Filtering and Prioritization:

- Filter feeds: damage, healing, CC effects, PvP objectives
- Prioritize important events (ultimates, major heals)

##### Chat Feed Integration:

- Seamless integration with ApeiroChatClient
- Auto-flag messages with offensive/toxic patterns

##### Event Logging:

- Timestamped PvP logs with GPS coordinates
- Logs archived by date and campaign

##### Encounter Summary Reports:

- Human-readable summaries for each PvP encounter
- Integrated with `ApeiroEncounterLog`

##### Performance Metrics:

- Real-time burst/sustain DPS/HPS tracking
- Session-end summaries and feedback

##### SavedVariables (in ApeiroFeedsLog)

```lua
["Feeds"] = {
  ["LogEnabled"] = true,
  ["ShowBurst"] = true,
  ["SoundAlerts"] = true
}
```

---

### 2.8 ApeiroQoL

This module provides quality-of-life improvements for PvP scenarios.

#### Features

- **Combat-state reticle mods**
- **Queue pop auto-accept**
- **Latency bar with alerts**
- **RDK-style Compass**
- **Auto Consumables**
- **Per-module Hide Out of Combat** (default: OFF)

Each module and visual UI element may be toggled to hide when out of combat. This setting is independently configurable per feature in the Apeiro settings panel.

#### Auto Consumables

Automatically consumes food/drink/war tortes when their effects expire.

```lua
["AutoConsumables"] = {
  ["Enabled"] = true,
  ["FallbackItem"] = "Pickled Fish Bowl",
  ["NotifyOnMissing"] = true
}
```

for PvP scenarios.

#### Features

- **Combat-state reticle mods**
- **Queue pop auto-accept**
- **Latency bar with alerts**
- **RDK-style Compass**
- **Auto Consumables**

#### Auto Consumables

Automatically consumes food/drink/war tortes when their effects expire.

```lua
["AutoConsumables"] = {
  ["Enabled"] = true,
  ["FallbackItem"] = "Pickled Fish Bowl",
  ["NotifyOnMissing"] = true
}
```

---

### 2.9 ApeiroBurstSync

Synchronizes group burst damage.

#### Features:

- Track delayed burst abilities
- Show ultimate readiness
- Damage Window for push timing
- Visual timers, icons, HUD overlay
- Replay-friendly UI for VOD review

---

### 2.10 ApeiroQualityOfLife (ApeiroQoL)

Defines the overarching QoL modules grouped within `ApeiroQoL`, including compass, combat state, revive assistance, and more.

Now continues with subsections 2.10.1 → 2.10.8.

---

### 2.10.1 ApeiroQoLCompass

Modelled after the popular RDK 'Yet Another Compass', ApeiroQoLCompass provides a highly configurable and informative compass that significantly improves player navigation and situational awareness in PvP environments.

**Key Features:**

- Fully customizable compass display, including size, colours, and distance from the reticle.
- Default configurations based on saved variables from `apeirogon@apeirogon` to streamline setup.
- Integration directly within the ApeiroQoL module to minimize module clutter.
- Option to toggle between standard directional markers and enhanced indicators showing notable strategic points (resources, keeps, outposts).

---

### 2.10.2 ApeiroQoLCombatState

The ApeiroQoLCombatState feature provides immediate and intuitive visual feedback of the player's combat state, enabling quicker reaction times and strategic decision-making.

**Key Features:**

- Customizable reticle colour change when entering or exiting combat.
- Reticle shape alteration options during combat state transitions.
- Optional modification of the outer border colour for ApeiroGrid modules (e.g., ApeiroGridSelf, ApeiroGridGroup) to clearly indicate combat state (default: red).
- Comprehensive settings in the Apeiro addon configuration panel to allow player-specific visual tuning and preferences.

---

### 2.10.3 ApeiroQoLCampStatus

The ApeiroQoLCampStatus function addresses strategic player respawn management, assisting in coordinated team actions and camp deployment decisions within Cyrodiil and Imperial City.

**Key Features:**

- Automatic detection and logging of player deaths without a nearby forward camp.
- Recording and logging precise GPS coordinates upon player death.
- Evaluating proximity to strategic locations such as keeps, resources, and towns.
- Maintaining an active list or dedicated UI window displaying "Camp Requests" associated with these strategic points.
- Directional assistance indicating the general orientation (north, east, south, west) relative to the nearest landmark to optimize camp placement and revive coordination.

---

### 2.10.5 ApeiroIgnoreEnhancer

This module enhances ESO's ignore system with tiered mute levels, visibility options, and optional warning overlays for PvP toxicity management.

#### Features

- Tiered ignore states:
  - Local Only (chat not hidden, just flagged)
  - Muted (chat hidden, no interaction)
  - HardBlocked (ESO-level ignore + system lockout)
- Chat display override:
  - Strikethrough or red text
  - Fully hidden if muted/hardblocked
- Optional visual warning:
  - Icon beside player name (e.g., ⚠️ or ߚ)
  - Configurable visibility scope (e.g., group only, always show)
- Works with `ApeiroChat` and auto-responder if enabled

#### SavedVariables (ApeiroChatSaved)

```lua
["IgnoreEnhancer"] = {
  ["ShowWarningIcons"] = true,
  ["MutedUsers"] = {
    ["@Toxic1"] = "Muted",
    ["@RepeatOffender"] = "HardBlocked"
  }
}
```

---

### 2.10.6 ApeiroAutoResponder (ON HOLD)

This module enables predefined automated responses to incoming whispers from flagged players. It is currently on hold pending review of ESO’s Terms of Service and Acceptable Use Policy.

#### Features (Planned)

- Triggered response to whispers from:
  - Players on mute list
  - Unregistered or unauthenticated players
- Message templates:
  - Per-tier response (e.g., "You have been muted", "Your behaviour has been flagged")
  - Per-player override if needed
- Safeguards:
  - Responds only if user is not typing
  - Rate-limited to prevent abuse or spam flagging
- Integration:
  - Works with ApeiroChat mute and block tiers
  - May sync auto-response templates from server if permitted

#### SavedVariables (ApeiroChatSaved)

```lua
["AutoResponder"] = {
  ["Enabled"] = false,
  ["Template"] = "Your message has been received, but you are muted in this session.",
  ["PerUser"] = {
    ["@RepeatOffender"] = "Due to prior conduct, messages from you will not receive a reply."
  }
}
```

---

### 2.10.7 ApeiroLagProfiler

This module provides real-time network latency analysis and retrospective diagnostics to help identify the root causes of disconnects, lag spikes, and performance anomalies — particularly in Cyrodiil or other large PvP contexts.

#### Features

- Real-time latency overlay:
  - Ping bar or numeric overlay
  - Optional warning flash on spikes above threshold
- Disconnection tracking:
  - Records timestamp, location, group size, and duration of frozen states
  - Optional server sync for aggregate analysis
- Historical graph panel:
  - Per-session ping trace
  - Detected packet loss indicators
- Integration:
  - Sends anonymized telemetry to `ApeiroServer` if enabled
  - Shows group-wide lag map overlay if `ApeiroGridGroup` is active

#### SavedVariables (in ApeiroCoreSaved)

```lua
["LagProfiler"] = {
  ["Enabled"] = true,
  ["WarningThreshold"] = 250,
  ["RecentDisconnects"] = {
    { time = 1720890000, reason = "Ping Timeout", map = "Cyrodiil" }
  }
}
```

---

---



