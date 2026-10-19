# Configuration

Edit `config.lua` and restart the resource after shared configuration changes. This is a free resource; the optional in-game charge is disabled by default.

## General

| Setting | Default | Purpose |
|---|---|---|
| `Framework` | `auto` | `standalone`, `qbox`, `qb` or `esx` can be selected explicitly |
| `Enabled` | `true` | Enables self-service access and menu commands |
| `Command / CommandAliases` | `scalemenu / scaleme` | Player commands |
| `OpenKey` | Empty | No default key; players can assign one in FiveM key bindings |
| `Units` | `metric` | Default metric or imperial display |
| `Locale` | `en` | Default UI language |
| `LiveApply` | `true` | Apply on slider release; false previews until Apply |
| `SmoothScaling / SmoothSpeed` | `true / 7.0` | Visual interpolation |
| `Notify` | `nui` | Built-in toast or `framework` notifications |
| `Debug` | `false` | Console diagnostics; no debug command is shipped |

## Height and Body Shape

Height limits default to **100–230 cm**, step **1**. Base heights are **182 cm male**, **170 cm female**, **178 cm fallback**. Use `Height.modelBaseCm` for addon model names, or adjust fallback bases. Stored height remains centimetres across model changes; the rendered factor is also limited by `Scaling.minFactor/maxFactor` (**0.55–1.35**).

Body shape is an index **0–100**, default **50**, with `Weight.spread = 0.15`: roughly 85–115% girth before hard width limits (**0.80–1.25**). It affects the whole rig. `Link.weightPerCm = 0.5` adjusts body shape when height changes while linked mode is active; linking is off by default.

Presets default to Slim **176/22**, Normal **180/50**, Heavy **182/85**, Athletic **186/42** (height/index). Add entries to `Config.Presets` and matching `preset_<id>` locale labels. Profiles default to **8** per character and **24** characters per name.

## Rendering and Preview

- `Scaling.rootHeight = 1.0` is the standing physics-origin-to-sole reference; calibrate it for unusual peds.
- `Vehicles.mode = 'hold'` suspends scale while entering/sitting/on vehicles or attached. `keep` is experimental.
- `FirstPerson.mode = 'unscaled'` uses normal local first-person size. `scripted` is an experimental eye-height camera; `native` leaves scaling active.
- Aiming pause and movement-rate adjustment are disabled; head-look suppression is enabled.
- Ghost and ruler are enabled. Ghost alpha is **90**, offset **1.1 m**. Preview camera distance is **3.4 m**, bounded **1.3–7.0 m**; adjust its other framing fields for your models.
- Remote scaling operates within approximately **120 m**; this is a rendering scope, not a persistence limit.

## Access

Empty identifier, job and role lists allow everyone. Nonempty lists are checked together: a player must pass each enabled restriction. Gender defaults to `both`. Sources are model, framework, then client hint; unknown gender defaults to deny when a restriction is active. For strict enforcement, remove the spoofable `client` fallback and configure authoritative model/framework data.

Discord roles need a server provider: `auto` tries a custom function then Badger_Discord_API; `custom` uses `discordCustom`; `badger` uses that resource; `none` supplies no roles. When a nonempty role list cannot be verified, access fails closed. Keep secrets in the provider's server configuration, never this shared file.

Admin ACE defaults to `gs_scalemenu.admin`. Self-service restrictions do not block trusted server override exports or admin commands. Reset remains available to restore normal size even if self-service access is later revoked.

## Storage and Cost

JSON directory is `data/scales`; autosave debounce is **1500 ms**. Keep the directory writable and back it up. Database mode is off; default table is `gs_scalemenu`. Failed pending writes are retried about every **30 seconds**, and close/drop/stop attempts a flush.

`Cost.enabled = false`, `amount = 0`, `account = 'bank'`. With costs enabled on a framework server, each accepted applied change is charged, including presets/profile loads. Reset is free. Standalone has no economy and applies changes freely.

## Hitbox

Capsule radius follows scale by default: base **0.25 m**, clamped **0.12–0.45 m**. Capsule height remains unchanged.

Bullet correction and phantom prevention are enabled. Defaults: **250 m** maximum distance (server tolerance 10 m), head/torso/limb multipliers **2.0/1.0/0.75**, global multiplier **1.0**, maximum **300** damage per report and **40 reports/second** per shooter. `boneSpace = 'scaled'` selects the sampled bone coordinate convention. `debugDraw` is false; temporarily enable it in configuration when checking alignment. See [Integrations](integrations.md) for limitations.
