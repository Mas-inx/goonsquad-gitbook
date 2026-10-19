# Integrations

## Framework Bridge

`shared/framework.lua` is editable and exposes identity, job, gender, roles, money, notifications and lifecycle hooks. Supported choices are standalone, Qbox (`qbx_core`), QBCore (`qb-core`) and ESX (`es_extended`).

Qbox/QBCore records use `cid:<citizenid>`; ESX uses `esx:<character identifier>`; standalone uses a raw identifier such as `license:...`. A framework character that is not loaded does not fall back to an account record. Changing framework/identifier rules needs a deliberate record migration.

The money bridge returns true only for a successful charge; ESX `cash` maps to its `money` account. A custom economy must keep its money operation synchronous and report failure correctly. Framework lifecycle handlers on the server listen locally; clients send the resource's own ready request for synchronization.

## Discord Roles

A custom role provider returns an array of role-ID strings, or nil when unavailable:

```lua
Config.Access.discordProvider = 'custom'
Config.Access.discordCustom = function(src)
    return exports['your-role-provider']:GetRoles(src)
end
```

This shared configuration function is called by the server. Keep credentials in the provider resource. A missing/erroring provider denies a role-restricted player. Built-in Badger_Discord_API support remains available.

## Clothing, Characters and Cameras

Scaling uses each ped's current model and reconciles model changes. Configure base heights and gender tables for addon peds. Other resources that rewrite matrices, force bone transforms or maintain scripted cameras may conflict. Test appearance reloads, character switches, ragdolls, vehicle attachment and first-person aiming together.

The ghost is a local clone and is removed when the preview closes or the resource stops. Resource stop releases owned cameras, focus and temporary bullet-proof overrides.

## Combat and Hitbox Limits

Correction runs on a shooter's client. It compares the shot segment with scaled and unscaled bone capsules. A reported additional hit is checked by the server for an online scaled target, same routing bucket, available living peds, held weapon, distance, finite/clamped damage and rate. The target client applies the accepted damage while honoring its invincibility/bullet-proof settings.

These checks bound a client report; they do not prove that a real bullet intersected that body. This resource is not an anti-cheat. Test with your weapon balance, damage handlers, armor, death system and instancing resources. Disable `Config.Hitbox.bulletCorrection.enabled` if another system must own bullet damage.

NPC fire, melee, explosions and projectiles are not corrected. Shotguns use one aim-line approximation. Scripted damage does not preserve normal attacker attribution or a normal shooter hit marker. Capsule radius can follow scale; capsule height and ragdoll/seat physics remain engine controlled.

## UI and Languages

The shipped HTML/CSS/JavaScript is already production ready. Runtime locale JSON remains editable. To add a language, provide matching keys/placeholders, extend the language list in `client/nui.lua` and the UI language registry, then rebuild the maintained production bundle. Current delivery includes six languages and 95 keys each. Those registry changes need a developer UI build; editing existing translations does not.
