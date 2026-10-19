# GS ScaleMenu

GS ScaleMenu is a **free** FiveM resource for changing character height and body shape with a live preview, a comparison ghost and a close-up orbit camera.

**Version:** 1.0.0 | **Author:** Goonsquad Studios | **Resource:** `gs-scalemenu` | **Price:** Free

## Features

- Height controls in centimetres or feet/inches, with a derived live readout and a 3D ruler
- Body-shape slider, linked proportions, smoothing and four configurable presets
- Eight named profiles per character by default
- Translucent comparison ghost showing the last confirmed size
- Live application on slider release, or manual preview/apply/discard
- Scale synchronization to other players and persistence across reconnects
- Identifier, job, Discord-role and gender access rules enforced on the server
- Optional per-change charges and ACE-protected administrator commands
- English, Spanish, German, French, Brazilian Portuguese and Russian
- Movement-capsule radius adjustment and configurable player-bullet correction

## Compatibility

| Component | Support |
|---|---|
| Frameworks | Standalone, Qbox, QBCore and ESX |
| Required server feature | OneSync |
| Default storage | Per-character JSON files |
| Optional database | oxmysql and MySQL/MariaDB |
| Browser UI | Included HTML/CSS/JavaScript; no customer build required |
| External assets | No separate map, model pack or inventory required |

Auto-detection selects a started Qbox core, then QBCore, then ESX, otherwise standalone. Start the intended core first or select it explicitly. Standalone storage follows a server identifier such as the license; framework storage uses character identifiers.

## Scaling Limits

Height is derived from each model's configured base height and a visual scale factor. Body shape scales the rig horizontally, including the face; it is not a kilogram weight or a per-bone morph.

The resource rewrites visible ped matrices each frame. Collision-capsule height, ragdoll physics and seat attachments remain engine controlled. Vehicles temporarily use normal size by default. The default local first-person view also uses normal size while other players retain your scale.

Bullet correction is a client-reported approximation for player instant-hit weapons. It is not an anti-cheat or a guarantee of perfectly scaled combat physics. NPC shots, melee, explosions and projectile weapons are outside that correction. See [Integrations](integrations.md) before enabling it alongside other combat resources.

Start with [Installation](installation.md), [Configuration](configuration.md) and [Usage](usage.md).
