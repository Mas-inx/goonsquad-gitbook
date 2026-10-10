# GS Baddie Hub

GS Baddie Hub gives your FiveM community a members lounge with profiles, friends, private messages, persistent live chat, starter claims and a season reward track. A city directory helps new residents discover businesses, events, careers and activities.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs-baddiehub`

{% hint style="info" %}
The release is prepared for FiveM Asset Escrow. Nine Lua files remain editable for configuration, translations, installation and integration. See [Configuration](configuration.md#editable-files).
{% endhint %}

---

## Features

- Welcome screen and eight preset profile portraits, item icons or approved HTTPS avatars
- Unique display names, bios, member search, friend requests and online presence
- Friends-only private messages by default, unread counts and read status
- Persistent community chat, optional word filtering, message deletion and blocking
- Server-checked character-gender and optional Discord-role access
- One-time starter pack and owned starter vehicle, per account or per character
- Ten configurable reward levels, daily login XP, limited chat XP and a usable XP pass
- Business and event directories, occupations, activities and GPS buttons
- Blush and Velvet Night themes with butterflies, petals and ambient effects
- English, Spanish, German, French and Brazilian Portuguese
- Qbox, QBCore, ESX or standalone adapters; optional activity webhooks

## Requirements

| Component | Requirement |
|---|---|
| Database | `oxmysql` and a working MySQL/MariaDB database; no JSON fallback |
| Server | FiveM GTA V with OneSync for server-side models, positions and starter vehicles |
| Framework | Qbox, QBCore, ESX or standalone |
| Inventory | Supported inventory/framework adapter or your standalone hooks for item rewards |
| Garage | A detected garage by default; ownership schema and garage name must match your server |
| UI | Included runtime assets; no npm installation on the server |

Standalone does not include a wallet, inventory or garage interface. Configure adapters or disable unsupported rewards. Directory entries are editable information: the hub does not create jobs, run races, schedule events or apply advertised business discounts. The season name is cosmetic; changing it does not reset progress.

Start with [Installation](installation.md), then [Configuration](configuration.md) and [Usage](usage.md). See [Integrations](integrations.md), [API & Exports](exports.md) and [Troubleshooting](troubleshooting.md).
