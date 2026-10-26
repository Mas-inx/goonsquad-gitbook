# GS Boombox + CarPlay

Portable speakers and a dashboard media system with shared server playback, portable speaker props, fitted screens and phone controls.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs-boombox-carplay`

## Features

- Five custom speakers and four mountable car screens, plus supported stock speaker models.
- Carry, place, recall and store speakers while keeping queues; linked speakers and car convoys.
- Music library, HTTPS audio links, YouTube search/playlists, live radio, favourites, shared queues and saved playlists.
- Positional audio, distance volume, wall/cabin muffling, effects, crossfade and floating now-playing displays.
- Dashboard maps, GPS, road reports, contacts/messages, vehicle controls, health information and car previews.
- Optional in-game phone apps, a real-phone browser remote and guest queue QR codes.
- Permission tiers, inventory items, a speaker shop, diagnostics, admin controls and persistent storage.

{% hint style="info" %}
Direct audio needs browser-compatible codecs and host permissions. YouTube and streams without CORS use distance volume without positional panning, effects or spectrum. Live radio does not have a shared seek position. See [Audio Sources & Limits](audio.md).
{% endhint %}

## Compatibility

| Component | Support |
|---|---|
| Framework | Standalone, Qbox, QBCore or ESX Legacy; detected automatically |
| Inventory | Built-in QBCore/ESX item APIs or `ox_inventory`; Qbox normally uses `ox_inventory` |
| Default speaker shop | Requires `ox_inventory`; disable it when using another inventory |
| Target | Optional `ox_target` / `qb-target`; proximity key fallback |
| Persistence | JSON files by default; optional `oxmysql` |
| State awareness | OneSync recommended; fitted screens and routing-bucket separation require it |
| Phones | Optional; see [Phones & QR Remotes](phones.md) |

No framework is required for standalone playback. To let ordinary players place speakers without an inventory, disable `Config.Boombox.item.required` and the shop. The default item requirement otherwise prevents ordinary placement.

The release includes prebuilt browser assets and streamed models; no npm install or asset generation is required. Seven explicit Lua files remain editable for configuration, framework/phone integration and notification wording.

## Getting Started

Follow [Installation](installation.md), configure [Inventory](inventory.md), then use [Commands & Usage](usage.md). Review [Configuration](configuration.md), [Permissions](permissions.md) and [Troubleshooting](troubleshooting.md) before opening access to players.
