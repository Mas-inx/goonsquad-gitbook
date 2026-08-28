# Tebex Integration

GS Weapon Designer can sell **designer passes** through your own Tebex store. A pass grants a limited designer session with a set number of weapon forges and, optionally, AI generations. Passes work without base access — players who buy one can design even on servers where the designer is otherwise job-locked.

All settings live in `shared/tebex.lua` under `GSWD.TebexConfig`.

---

## Enabling

| Option | Default | Description |
|---|---|---|
| `Enabled` | `false` | Master switch for the pass system |
| `ClaimCommand` | `'gswd_claim_designer'` | Command players use to claim a purchased pass |
| `GrantCommand` | `'gswd_tebex_grant'` | Command your Tebex package runs to deliver a pass |
| `AutoOpenOnPurchase` | `true` | Open the designer immediately when an online player's purchase is delivered |
| `NotifyPendingOnJoin` | `true` | Tell players on join that they have an unclaimed pass |

---

## Packages

```lua
Packages = {
    starter = {
        Label = 'Starter Designer Pass',
        TebexPackageId = 'replace_with_tebex_package_id',
        TebexPackageName = 'Starter Designer Pass',
        WeaponAmount = 1,
        AIGenerations = 5,
    },
    premium = {
        Label = 'Premium Designer Pass',
        TebexPackageId = 'replace_with_tebex_package_id',
        TebexPackageName = 'Premium Designer Pass',
        WeaponAmount = 3,
        AIGenerations = 20,
    },
}
```

| Setting | Description |
|---|---|
| `Label` | Name shown to players in-game |
| `TebexPackageId` | The package ID from your Tebex store |
| `TebexPackageName` | The package name as it appears on Tebex |
| `WeaponAmount` | Weapon forges included in the pass |
| `AIGenerations` | AI generations included in the pass (requires the `gswd-ai` addon) |

Add as many packages as you like — the keys (`starter`, `premium`, ...) are the package references used by the grant command.

---

## Tebex Store Setup

On each Tebex package, add a **game server command** that runs the grant command with the buyer and transaction:

```text
gswd_tebex_grant starter {id} {transaction}
```

- If the buyer is online, the pass is delivered instantly and, with `AutoOpenOnPurchase`, the designer opens for them.
- If the buyer is offline, the entitlement is stored. They claim it later with `/gswd_claim_designer`.

Entitlements persist in the database, so passes survive restarts and are never lost to a missed delivery.

{% hint style="info" %}
Staff can also deliver passes manually: run `gswd_tebex_grant <package> <player id or identifier> <transaction id>` from the server console, or in-game with the `gswd.tebex` ACE.
{% endhint %}

---

## Consumption Rules

```lua
Usage = {
    ConsumeWeaponOn = 'forge',
    RequireRemainingWeaponsToClaim = true,
    RequireRemainingWeaponsForAI = true,
}
```

| Setting | Description |
|---|---|
| `ConsumeWeaponOn` | `'forge'` deducts a weapon credit when the design is published; `'save'` deducts on first save instead |
| `RequireRemainingWeaponsToClaim` | Players cannot open a pass session with zero weapon credits left |
| `RequireRemainingWeaponsForAI` | AI generations can only be used while weapon credits remain |

Limited sessions can only edit designs created within that session, and failed AI generations refund their credit automatically.
