# Goonsquad Custom Sirens

Server-synced LEO vehicle sirens with NUI 3D positional audio for FiveM.

**Version:** 1.0.0 | **Price:** TBD

---

## Features

### Siren System
- 20+ unique siren tones including Wail, Yelp, Phaser, Hi-Lo, Airhorn, Powercall, Q Siren, Rumbler, and more
- 5 configurable siren slots per vehicle (NUMPAD1-5 defaults)
- Hold (momentary) and toggle siren modes
- Stop all key (NUMPAD0 default)
- Per-vehicle siren configuration

### Audio Engine
- NUI-based 3D positional audio using Web Audio API
- Emitter-based spatial audio with distance attenuation
- Occlusion support with low-pass filtering
- Per-siren volume, pitch, reference distance, max distance, and rolloff
- Inside-vehicle volume multiplier
- Configurable listener mode (camera or player)

### Vehicle Integration
- Per-model vehicle configuration
- Native siren suppression (mutes GTA default sirens on configured vehicles)
- Automatic emergency lights with custom siren activation
- Per-vehicle volume multiplier
- Occupant/driver/passenger access control
- Engine-on requirement option
- Auto-stop on vehicle destruction

### Framework Support
- Job-locked siren access (configure which jobs can use sirens)
- Automatic framework detection (QBCore, ESX, Qbox)
- On-duty enforcement option
- Open hook files for custom permission logic

### Performance
- Server-authoritative state synced via state bags
- Client-side emitter tracking with stale timeout
- Discovery scan for newly streamed siren vehicles
- Configurable update intervals
- Cooldown system to prevent spam

---

## Requirements

| Dependency | Notes |
|------------|-------|
| OneSync | Required for state bag syncing |

---

## Quick Start

See [Installation](installation.md) for setup instructions.
