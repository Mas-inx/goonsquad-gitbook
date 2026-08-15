# Changelog

All notable changes to Goonsquad Studios resources will be documented here.

## GS Announcements 2.6.0

- Added the full announcement Builder with seven layouts, animated backgrounds, media, lightshows, sizing, sounds, GPS actions, and scheduling.
- Added limited player publishing with job locking and an administrator approval queue.
- Added MySQL-backed access control for jobs, players, one-time passes, super admins, minimum grades, and content restrictions.
- Added an approved asset-library workflow for announcement backgrounds, logos, and artwork.
- Added profiles, broadcast history, recurring schedules, business statuses, CSV history export, and live engagement analytics.
- Added Qbox, QBCore, and ESX job detection with standalone administrator operation.

## Zombies V3 3.1.0

- Added named zombie profiles with weighted models and per-profile stats, combat, movement speed, and locomotion.
- Added profile zones with weighted links and deterministic overlap priority.
- Added ox_lib-compatible sphere, box, and polygon/prism zones with a built-in fallback when ox_lib is unavailable.
- Added server exports for runtime profile, profile-zone, profile-link, and safe-zone management.
- Added client exports for managed-zombie detection and profile inspection.
- Extended client and server spawn hooks with backward-compatible profile context.

## ClothDesigner 1.3.0 RC

- Added limited designer client and server exports with separate clothing and AI allowances.
- Added persistent Tebex package entitlements with configurable package limits, online auto-open, offline claiming, transaction de-duplication, and usage rules.
- Added a player wardrobe for previewing, wearing, and removing created clothing.
- Added optional admin approval and rejection before a print is published and granted.
- Added synchronized equip and unequip state across inventory items and the wardrobe.
- Made printed inventory items toggle their design on first use and off on second use.
- Added persistent previous-appearance restoration for clothing components and props, including footwear.
- Improved reconnect rehydration so equipped runtime textures return after supported framework load events.
- Added FiveManage media hosting alongside Discord and database storage.
- Added pre-`FileReader` client upload-size validation with player-facing errors.
- Added a bounded media queue for uploads, imports, design previews, AI references, and generated-media hosting.
- Added AK47 Inventory support and expanded inventory documentation for Jaksam, CodeM, Quasar Advanced Inventory, and ox_inventory.
- Added a persistent black or light-gray 3D preview background switch.
- Added lazy loading and unloading for off-screen library previews.
- Reworked WebGL renderer lifecycle to reuse and dispose contexts correctly.
- Improved generated pack diagnostics with exact source YDD, companion YTD, and generated YDD names for invalid references.
- Added stale retry invalidation to prevent removed clothing from immediately reappearing.
- Reduced pants flicker with prebinding and a targeted component-cache fallback.

## [Unreleased]

### ClothDesigner
- Documentation and release-candidate validation

### DarkMet
- Initial release

### Zombies V3
- Initial release

### Paintball
- Initial release

### Fueling System
- Initial release

### Arenas
- Initial release

### Multicharacter (QBCore)
- Initial release

### Characters (ESX)
- Initial release

### Leveling System
- Initial release

### Zombies/Cannibals
- Initial release
