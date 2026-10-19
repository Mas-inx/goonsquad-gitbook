# GS Nails

GS Nails brings a working nail salon and a custom nail-art studio to FiveM. Seat customers, preview sets on their hands, create designs with a layered editor and collect cash or bank payments.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs_nails`

{% hint style="info" %}
The default five manicure stations are aligned to the separately installed Fable nail salon interior near -590, -1134. The interior is not included. Female nails are enabled by default; the included male pack can be enabled in configuration.
{% endhint %}

---

## Features

### Salon Roleplay

- Five paired customer armchairs and technician stools with synchronized occupancy
- Custom sit-down, manicure and stand-up animations and a technician's brush prop
- Close-up hand cameras and live previews for both session participants
- Presets, a shared salon Lookbook and the customer's personal designs
- Technician-set charges, cash/card prompts, payment expiry and cancellation
- Four automatically registered job grades, optional duty requirement and boss-menu integration
- Paid nails persist in character metadata and return after supported appearance reload events

### Custom Nail Studio

- Six shapes: Almond, Coffin, Long Coffin, Oval, Square and Stiletto
- Eighteen stock presets per gender and 398 custom texture slots per gender
- Base colors and gloss, matte, chrome, glitter and pearl finishes
- Ombré, French tips, patterns, stickers, text, image import, brush and eraser
- Layer editing, transforms, undo/redo, and editing saved designs as copies
- Self-design from manicure armchairs; optional separate kiosks are preview/save only
- Personal and salon libraries, with ownership and staff checks on the server
- Database storage by default; optional Discord or FiveManage image hosting with database fallback
- Inspect/show gestures with keys and optional chat commands

One painted design appears on all ten fingers. Custom slots are shared by people wearing the same design; capacity is per shape and concerns distinct designs being worn or previewed at once. Personal libraries default to 30 active designs per character. Slot counts do not mean unlimited personal storage.

## Compatibility

| Component | Requirement |
|---|---|
| Framework | Qbox / QBCore / ESX Legacy |
| Target / notifications | `ox_target` / `ox_lib` |
| Database | `oxmysql` and MySQL/MariaDB |
| Characters | Female/male freemode peds |
| Boss menu | Optional `qbx_management`, `qb-management` or `esx_society` |
| State awareness | OneSync enabled |

`Config.Framework = 'auto'` selects a running core in this order: Qbox, QBCore, ESX. If none is running, detection falls back to an installed core. Start your chosen framework before GS Nails; set `qbox`, `qb` or `esx` explicitly when multiple cores are installed. A supported framework is required.

The release contains finished streamed packs and production browser assets. No npm installation, runtime asset generation or raw nail templates are needed. Five explicit Lua files remain editable for configuration and integration; the other Lua files are prepared for Cfx portal processing.

## Quick Start

1. Follow [Installation](installation.md).
2. Set locations, gender packs and access in [Configuration](configuration.md).
3. Learn the salon and editor flows in [Usage](usage.md).
4. Review [Integrations](integrations.md), [API & Exports](exports.md) and [Troubleshooting](troubleshooting.md).
