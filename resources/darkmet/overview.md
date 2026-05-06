# Goonsquad DarkMet

A feature-rich, immersive dark-web trading script for FiveM servers. Supports **QBCore**, **QBox**, and **ESX** out of the box.

**Price:** $29.99 | [Buy on Tebex](https://goonsquad-inc.tebex.io/package/7365474) | [Watch Preview](https://www.youtube.com/watch?v=0SsuHWrsMx0)

---

## Features

### UI/UX
- Non-fullscreen windowed laptop UI (900x620 px)
- CRT scanline + flicker + vignette + grain overlay effects
- Animated terminal boot sequence (TOR routing, encryption, etc.)
- Phosphor-green-on-black cyberpunk aesthetic
- Live clock display, heat meter, connection status
- Glitch animation on title bar every ~8 seconds
- Fully previewable in any browser (demo mode)

### Marketplace
- Paginated listing grid with category filters
- Search bar (item name or description)
- Item condition tiers: Mint / Good / Used / Damaged
- Listing detail modal with fake TX hash
- Create listing form with preview before posting
- Configurable price floors/ceilings per item
- Maximum listings per player configurable

### Economy
- Escrow system holds buyer funds until delivery confirmed
- Auto-release escrow after configurable hours
- Configurable marketplace tax rate
- Crypto wallet UI with animated balance counter
- Coin Mixer with fake node-routing animation
- Seller payout to wallet if offline when delivery confirmed

### Communication
- Encrypted DMs between players (alias-to-alias)
- Auto-deleting messages (configurable TTL)
- New message toast notification
- Unread message badge on nav
- Quick-message a seller directly from listing modal

### Security & Risk
- Heat level system (browsing/listing/buying raises heat)
- Laptop tier affects heat gain (Elite = 0.4x multiplier)
- Heat threshold triggers police notification
- Honeypot listings (configurable chance)
- Honeypot purchase triggers raid alert
- Zone-restricted access (PolyZone areas)
- Per-player alert cooldown to prevent spam

### Identity
- Alias + password account system
- Anonymous handles decoupled from citizenid
- Reputation skulls based on completed sales
- User reporting system
- Verified badge tier labels

### Laptop Integration
- Spawns prop on player's hand when UI opens
- Typing animation while UI is open
- Three laptop tiers (Standard, Pro, Elite)
- Different boot delay, heat multiplier, and listing slots per tier
- Location-based access restrictions (optional)

---

## Framework Support

| Framework | Status |
|-----------|--------|
| QBCore | Full support |
| QBox | Full support |
| ESX | Full support |
| Auto-detection | Included |

---

## Requirements

| Dependency | Notes |
|------------|-------|
| `oxmysql` | Database connector (required) |
| `qb-core` | If using QBCore framework |
| `qb-inventory` | If using QBCore |
| `ox_inventory` | If using QBox framework |
| `es_extended` | If using ESX framework |

---

## Quick Start

See [Installation](installation.md) for full setup instructions.
