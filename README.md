# Apeiro ESO PvP Addon Suite

Welcome to the official repository for the **Apeiro ESO PvP Addon Suite**, a modular and advanced PvP support system designed for Elder Scrolls Online (ESO) — Cyrodiil, Imperial City, Battlegrounds, and beyond.

This repository contains the full codebase and specifications for all Apeiro addon modules. All components conform to the [CSA Cloud Controls Matrix](https://cloudsecurityalliance.org/research/cloud-controls-matrix) and [COBIT](https://www.isaca.org/resources/cobit) principles, and adhere to a consistent modular architecture.

---

## 📖 Specification

The complete specification is located in:

```
ApeiroESOAddonSpec.md
```

This document defines **every module**, **feature**, and **naming convention**. All development tasks should trace directly to this file.

---

## 🧱 Folder Structure

Each module resides in its own folder prefixed with `Apeiro`. For example:

```
ApeiroCore/
ApeiroGridSelf/
ApeiroGridGroup/
ApeiroEffects/
ApeiroFeeds/
...etc
```

Refer to Section 2 of `ApeiroESOAddonSpec.md` for the full list and detailed descriptions.

---

## 🧠 Codex Instructions (For GitHub Copilot or AI agents)

To begin developing from this repository using Codex, GitHub Copilot, or any other LLM-based tooling:

1. **Parse** `ApeiroESOAddonSpec.md` to understand module structure, naming rules, and compliance requirements.
2. **Scaffold the folder layout** based on Section 2:
   - One folder per module (e.g., `ApeiroGridSelf`, `ApeiroEffects`, etc.)
   - All names must begin with `Apeiro`
3. Create stub .lua files inside each folder — for example, ApeiroEffects.lua in the ApeiroEffects/ folder.
4. Implement the file ApeiroCore.lua first. It contains shared systems like SavedVariables, module loading, and combat state hooks (see Section 2.1).
5. **Avoid guessing.** If a section is unclear, defer to the README prompt context (below).

This instruction block is machine-readable and bounded by `<!-- codex:start -->` and `<!-- codex:end -->`.

---

## 🚀 Project Goals

- Replace and extend legacy PvP addons with a unified, configurable suite
- Incorporate selectively inspired features from a wide range of existing ESO PvP addons. When only a subset of an addon's capabilities aligns with the Apeiro vision, those components are re-implemented as clean, modular subsystems within the Apeiro architecture. In all such cases, we credit the original addon as the source of inspiration while rebuilding functionality to meet Apeiro's consistency, configurability, and compliance standards.
- Enable real-time performance monitoring, coordination, and review
- Build features like:
  - Dynamic combat grids
  - Buff/debuff tracking
  - AP gain logging
  - Sync-based burst coordination
  - Proximity radar

---

## 🔧 Client & Agent Integration

The Apeiro addon suite is complemented by two additional components hosted in separate repositories:

- [`apeiro-agent`](https://github.com/apeirodev/apeiro-agent)
- [`apeiro-infra`](https://github.com/apeirodev/apeiro-infra)

Together, they enable enhanced functionality such as:

- GPU-accelerated encounter parsing and moderation
- Long-term chat log storage and Discord integration
- Config and telemetry syncing across devices
- Optional AI-powered feedback and smart auto-response tools
- Server-side analysis of AP trends, burst timing, revive opportunities, and more

While not required for local addon use, these components extend the Apeiro suite into a complete PvP coordination and insight system.

---

## 🧩 Dependencies

The following ESOUI libraries are required or recommended for full functionality (we recommend using the [Minion](https://minion.mmoui.com/) application to facilitate installation):

- [LibAddonMenu-2.0](https://www.esoui.com/downloads/info7-LibAddonMenu.html)
- [LibMainMenu-2.0](https://www.esoui.com/downloads/info2289-LibMainMenu-2.0.html)
- [LibMediaProvider-1.0](https://www.esoui.com/downloads/info28-LibMediaProvider-1-0.html)
- [LibChatMessage](https://www.esoui.com/downloads/info1925-LibChatMessage.html)
- [LibMapPins-1.0](https://www.esoui.com/downloads/info1885-LibMapPins.html)
- [LibGPS](https://www.esoui.com/downloads/info2285-LibGPS.html)
- [LibCustomMenu](https://www.esoui.com/downloads/info1845-LibCustomMenu.html)
- [LibGroupSocket](https://www.esoui.com/downloads/info1724-LibGroupSocket.html)
- [LibCombat](https://www.esoui.com/downloads/info1994-LibCombat.html)
- [LibSavedVars](https://www.esoui.com/downloads/info2090-LibSavedVars.html)
- [LibDebugLogger](https://www.esoui.com/downloads/info1360-LibDebugLogger.html)

All dependencies are managed via ESO’s `Lib*` libraries. See `ApeiroESOAddonSpec.md` for integration notes. No external API or cloud service is required unless explicitly declared.

---

## ✅ Suggested Companion Addons

These addons are not required for the Apeiro suite to function, but we highly recommend them for a more polished and robust PvP experience:

- [pChat](https://www.esoui.com/downloads/info93-pChat.html) — Enhances chat functionality, formatting, timestamps, and tabs.

---

## 🛡 Compliance

This project adheres to:

- 🇨🇦 [Canadian spelling](https://www.canada.ca/en.html)
- [CSA Cloud Controls Matrix](https://cloudsecurityalliance.org/research/cloud-controls-matrix) (Security)
- [COBIT Framework (ISACA)](https://www.isaca.org/resources/cobit) (IT Governance)
- [ESO Terms of Service](https://www.elderscrollsonline.com/en-us/terms-of-service) (no automation violations)

---

## 🧰 Installation

### 🔧 Manual Installation

1. Download or clone this repository.
2. Copy each `Apeiro*` folder into your ESO AddOns directory:
   ```
   Documents\Elder Scrolls Online\live\AddOns\
   ```
3. Ensure required libraries are installed (see Dependencies).

### 🚀 Automated Installation (coming soon)

Once the Apeiro suite reaches full release, it will be available via:

- [ESOUI](https://www.esoui.com/) (Addon download portal)
- [Minion](https://minion.mmoui.com/) (Addon manager and updater)

You’ll be able to install and update the suite with a single click via Minion.

---

## 📝 License

This project is private and proprietary. Redistribution is not permitted without explicit written consent from the project owner.

---

## 🤝 Contributing

Contributions to this repository are restricted. However, we actively welcome feedback, suggestions, and issue reports.

- Use the **GitHub Issues** tab to report bugs, suggest enhancements, or ask for clarification.
- Use the **GitHub Discussions** (if enabled) or the linked Discord integration for open questions or commentary.

If you have code to contribute, please contact the maintainer directly before submitting a pull request.

---

## 🙋 Questions?

Please refer to `ApeiroESOAddonSpec.md` first. If unsure about interpretation or implementation, prompt an assistant or project AI using this context:

```
Act as lead developer on the Apeiro PvP addon. Follow the spec precisely. Use consistent Apeiro naming. Ask before assuming.
```

---

🌀 Apeiro is a Möbius-inspired PvP ecosystem — elegant, seamless, infinite.

