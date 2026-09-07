# Changelog

All notable changes to Goonsquad Studios resources will be documented here.

## GS Map Studio 1.0.0

- Added the in-game map editor with a noclip freecam, FLY and EDIT camera modes, ped walk mode, focus-on-selection, and go-to-coordinates.
- Added ghost placement with surface alignment, a 3-axis Move / Rotate / Scale gizmo with world and local space, grid, angle, and scale snapping, and arrow-key nudging.
- Added the Array tool (line, grid, circle, path, stairs), the Scatter brush with weighted models, Polygon fill, Area select, Eyedropper, and World erase.
- Added World Edit: select, move, and delete GTA's own map props at runtime using engine model hides, including static geometry by typed model or hash, with per-map restore.
- Added dynamic point and spot lights with color, range, brightness, cone controls, pulse, flicker, and strobe animation, optional shadow, and attachment to props.
- Added layers, groups, server-shared prefabs, copy and paste, align and distribute, and unlimited undo and redo with a browsable history page.
- Added the prop library with categories, search, favorites, recently used, collections, direct spawn by model name, and `Config.CustomProps` for add-on packs.
- Added server-side JSON file storage with rolling autosave, crash recovery, and Locked, Public, and Autoload flags per map.
- Added live co-editing with validated, rate-limited operation batches and automatic resync on rejection.
- Added export to YMAP XML, JSON, Lua, and CSV, and one-click publish of a standalone streaming resource.
- Added Qbox, QBCore, ESX, and standalone support with ace, identifier, and job-based permissions, six interface languages, dark and light themes, and rebindable keys.

## GS Appearance 2.0.0

- Added the cinematic appearance editor: arc category rails, a curved item carousel with real preview renders, a drag dial for mixes and sliders, a 64-swatch hair palette, and a scripted camera with face, shirt, pants, and shoes focus presets.
- Added the full freemode pipeline: ped models, clothing components, props, head blend, 20 face-feature sliders, 12 head overlays, hair with tint, eye color, makeup, and tattoos by body zone.
- Added four store types — clothing, barber, tattoo, and surgeon — with database-backed zones, per-type blips and prompts, job and gang locks, and server-side distance and cost enforcement.
- Added `Config.StoreMenus`, so each shop type's customization sections are configurable and a single section skips the menu entirely.
- Added the in-game admin dashboard: an interactive store map with create, move, toggle, and delete, a live server-wide accent color, and item rules with blacklist, job lock, and player whitelist types enforced on the server.
- Added named outfits with in-place updates and verified writes, shareable outfit codes, and job and gang uniforms filtered by job, grade, and gender.
- Added drop-in compatibility with `illenium-appearance`, `qb-clothing`, `skinchanger`, `esx_skin`, and `fivem-appearance`, including their exports, events, and callbacks.
- Added an ox_lib-free callback bridge that reimplements ox_lib's wire protocol, so third-party scripts keep working on servers that do not run ox_lib.
- Added automatic migration of `qb-clothing` and `esx_skin` / `skinchanger` skin formats, so existing characters keep their look and are never sent back through the character creator.
- Added Qbox, QBCore, ESX, ox, and standalone detection with late-start recovery, plus character-creation hand-off for `qb-multicharacter`, `qbx_properties`, `esx_multicharacter`, and `esx_identity`.
- Added automatic schema creation and self-healing migrations, including widening `player_outfits.citizenid` for ESX Legacy identifiers and adding the unique key non-destructively.
- Added CDN preview hosting, removing the ~7,900-file registration that made `ensure gs_appearance` hang. `Config.CaptureBaseUrl` repoints every preview at another account or host in one line, with a per-file url map as a fallback for hosts that do not mirror the folder layout.
- Added `server/credentials.lua`, a server-only file holding the Fivemanage upload key and endpoint, and a resumable upload tool that reads them and prints the base url to paste into the config.
- Added `Config.IconOverrides`, which swaps any individual UI icon for a resource file or a hosted image without rebuilding the NUI, falling back to the shipped icon if an override fails to load.
- Added startup conflict detection for competing appearance resources, the `/gsdiag` install diagnostic with a real database write round-trip, and `/gsrelease` as an emergency NUI and ped release.

## GS Weapon Designer 0.2.0

- Added the in-game weapon skin studio with a layer-based canvas, UV wireframe guides, live 3D preview, and per-part editing for bodies, magazines, suppressors, scopes, grips, and flashlights.
- Added the slot system: every published design occupies a pre-built addon weapon slot, with per-weapon counts and a server-wide ceiling controlled by `data/weapon_slots.json`.
- Added runtime skin rendering visible to nearby players, with persistence across disconnects and server restarts.
- Added 29 vanilla weapon templates covering rifles, pistols, SMGs, shotguns, and melee weapons.
- Added the armory with design history, restore, equip, and delete, plus an optional staff approval queue with audit logging.
- Added ox_inventory integration with real weapon items and working attachments via the bundled bridge module, plus support for QBCore, Qbox, ESX, Jaksam, CodeM, Quasar, and AK47 inventories through a ticket item.
- Added Tebex designer passes with weapon and AI-generation allowances, offline claiming, and persistent entitlements.
- Added the studio tool API for external addons, including the optional `gswd-ai` generation companion.
- Added Qbox, QBCore, and ESX detection with a standalone fallback, and automatic database migration on first start.

## GS Garages 1.0.0

- Initial release with public, house, job, gang, and impound garages, all defined as data in `config/garages.lua`.
- Added the showroom interface with a live 3D turntable preview, composed camera presets, cinematic mode, and real handling-data performance bars.
- Added in-world DUI surfaces: the nameplate behind the previewed vehicle, the `E — PARK` prompt at garage bays, and the `E — OPEN GARAGE` prompt on foot.
- Added Qbox, QBCore, ESX, and standalone support with automatic framework detection and no framework file edits.
- Added auto-detected fuel, vehicle key, and target adapters, each with a config override hook.
- Added automatic house garages for ps-housing, qbx_properties, qs-housing, qb-houses, loaf_housing, esx_property, and rtx_housing, plus a custom provider hook.
- Added favorites, nicknames, transfers, give-keys with persistent shared access, "find my car" waypoints, and odometer mileage.
- Added impound lots with job-gated release, depot pricing, and police/tow exports.
- Added the in-game admin suite: world-placed garage creation and live, database-persisted config editing.
- Added additive idempotent schema migrations, a native import for incomplete dealership rows, and cross-script importers for qb-garages, jg-advancedgarages, cd_garage, and loaf_garage.
- Added server-side ownership, fee, rate-limit, and duplicate-spawn protection, plus batched Discord webhook logging.

## GS Announcements 1.0.0

- Added the full announcement Builder with seven layouts, animated backgrounds, media, lightshows, sizing, sounds, GPS actions, and scheduling.
- Added limited player publishing with job locking and an administrator approval queue.
- Added MySQL-backed access control for jobs, players, one-time passes, super admins, minimum grades, and content restrictions.
- Added an approved asset-library workflow for announcement backgrounds, logos, and artwork.
- Opened Asset Library submissions to whitelisted players; media stays pending until an approver signs it off.
- Added `Config.PlayerMode.RequireApproval` to switch player publishing between the admin approval queue and direct broadcasting.
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
