# Goonsquad Zombies V3

Advanced zombie survival system for FiveM. Standalone — no framework required, but supports ESX, QBCore, and Qbox.

**Price:** $10.00 (was $35) | [Buy on Tebex](https://goonsquad-inc.tebex.io/package/7404344) | [Watch Preview](https://www.youtube.com/watch?v=e5gFJBduIQQ)

---

## Features

### Zombie AI
- Sight-based and hearing-based perception
- Smart aggro with line-of-sight checks
- Wandering, alert, chase, and attack states
- Custom attack animation mode with timed damage
- DLC zombie native melee support (Bottom Dollar Bounties)
- Legacy GTA zombie ped support
- Configurable combat attributes and movement

### Infected Animals
- Cougar, dog, and boar variants
- Regional spawn restrictions
- Weighted spawn system
- Custom per-animal audio banks
- Configurable spawn chance

### Vehicle Interaction
- Zombies pressure players inside vehicles
- Vehicle damage system (body + engine health)
- Drag-out system with configurable chance
- Per-vehicle seat detection for door opening
- Server-validated attacks

### Audio System
- Built-in NUI-based 3D positional audio
- Per-ped spatial audio emitters
- Occlusion with volume/low-pass filtering
- Pitch variance for organic sound
- Categorized audio per state (idle, alert, chase, attack, hit, death)
- Separate banks for human, animal, vehicle, horde, and corpse sounds
- xsound alternative backend support

### Performance & Cleanup
- Living zombie distance cleanup
- Dead zombie delayed cleanup with corpse despawn effects
- Routing bucket population suppression
- World population control (traffic, peds, cops, boats, garbage)
- Adjustable spawn caps and intervals
- Melee combat limits tuning

### Loot System
- Configurable loot rewards with chance
- ox_target / qb-target integration
- ox_inventory / qb-inventory / qb-core support
- Custom loot override via open file hooks
- Dead zombie target interaction

### Corpse Despawn
- Multiple effect modes: instant, fade, sink, sink_fade
- Configurable duration, delay, and sinking distance
- Collision disable and freeze options
- Fade alpha control
- Per-corpse despawn audio cues

---

## Framework Support

| Framework | Status |
|-----------|--------|
| Standalone | Full support |
| QBCore | Full support |
| QBox | Full support |
| ESX | Full support |

---

## Requirements

| Dependency | Notes |
|------------|-------|
| OneSync | Required |
| Game build 3258+ | Bottom Dollar Bounties DLC support |
| Lua 5.4 | Recommended |
| ox_target / qb-target | Optional (for loot interaction) |
| ox_inventory / qb-inventory | Optional (for loot system) |

---

## Quick Start

See [Installation](installation.md) for setup instructions.
