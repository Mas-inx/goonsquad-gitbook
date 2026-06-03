# Goonsquad Player Rewards

A season battlepass / playtime rewards system for FiveM. Players earn rewards based on hours played across Free and Prime tracks.

**Version:** 1.0.0 | **Price:** TBD

---

## Features

### Season Battlepass
- Dual-track system: **Free** and **Prime** reward lanes
- Configurable tiers with hour-based unlock requirements
- Rewards: cash, items, weapons, vehicles, clothing, XP, and custom
- Overseer in-game admin panel for full season management
- Push-live editing with broadcast to all open ledgers

### Playtime Tracking
- Automatic session tracking with configurable save intervals
- Minimum session threshold to prevent micro-session abuse
- Live hour counter synced to UI while ledger is open
- Audit log of session history

### Prime System
- **Tebex mode** — players purchase Prime via store checkout
- **In-game mode** — players buy Prime with in-game cash/bank
- **Discord auto-grant** — sync Prime from Discord role
- **Admin commands** — grant/remove Prime

### Overseer Admin Panel
- Full season editor with reward tier management
- Catalog picker for items, weapons, and vehicles
- Reorder rewards with drag/arrows
- Season meta editor (name, tag, tier count)
- Tebex relay configuration
- In-game Prime pricing
- Unsaved changes warning

### Framework Support
- Auto-detects QBCore, ESX, Qbox, or standalone
- Supports ox_inventory, qb-inventory, and framework-native inventories
- Money abstraction for cash/bank accounts
- Vehicle key integration (qb-vehiclekeys, qbx_vehiclekeys, okokGarage)

### UI
- React 19 + TypeScript NUI
- Operator portrait with gender detection
- Live session counter
- Claim receipt ticker
- Reward detail modal
- Prime purchase modal

---

## Requirements

| Dependency | Required | Purpose |
|------------|----------|---------|
| oxmysql | Yes | Database |
| ox_lib | Client | Notifications, URL opening |
| OneSync | Yes | State management |

---

## Quick Start

See [Installation](installation.md) for setup instructions.
