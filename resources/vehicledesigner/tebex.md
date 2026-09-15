# Tebex Integration

Tebex packages can grant persistent, limited designer passes. Each package defines how many liveries a player may print and how many AI generations they may use.

---

## How It Works

1. Tebex runs a console command after a purchase.
2. GS Vehicle Designer creates an entitlement in MySQL using the package limits from `shared/tebex.lua`.
3. An online buyer can be taken directly into the studio.
4. An offline buyer claims the entitlement after joining.
5. Printing and AI usage decrement the persistent entitlement.

Livery allowance is consumed when a design is printed. Reprinting the same design within the same session does not consume a second use. AI allowance is consumed by the registered AI provider when generation starts.

---

## 1. Enable Tebex

Open `shared/tebex.lua` and set:

```lua
GSVD.TebexConfig = {
    Enabled = true,
    ClaimCommand = 'gsvd_claim_designer',
    GrantCommand = 'gsvd_tebex_grant',
    AutoOpenOnPurchase = true,
    NotifyPendingOnJoin = true,
}
```

| Setting | Description |
|---|---|
| `Enabled` | Master switch for package grants and claims. Ships as `false`. |
| `ClaimCommand` | Player command for opening the next available entitlement. |
| `GrantCommand` | Console command used in Tebex package actions. |
| `AutoOpenOnPurchase` | Opens the studio immediately when the buyer is online. |
| `NotifyPendingOnJoin` | Notifies a player roughly 15 seconds after joining when a package is waiting. |

---

## 2. Create Package Mappings

Add one config entry for every Tebex package:

```lua
Packages = {
    starter = {
        Label = 'Starter Livery Pass',
        TebexPackageId = 'replace_with_tebex_package_id',
        TebexPackageName = 'Starter Livery Pass',
        LiveryAmount = 1,
        AIGenerations = 3,
    },

    premium = {
        Label = 'Premium Livery Pass',
        TebexPackageId = 'replace_with_tebex_package_id',
        TebexPackageName = 'Premium Livery Pass',
        LiveryAmount = 5,
        AIGenerations = 15,
    },
}
```

| Field | Description |
|---|---|
| Config key | Internal short name such as `starter`. It can be used in commands and exports. |
| `Label` | Player-facing package label. |
| `TebexPackageId` | Package ID from the Tebex store. |
| `TebexPackageName` | Optional additional name that can resolve the package. |
| `LiveryAmount` | Liveries the pass can print. |
| `AIGenerations` | Generations available to a registered AI or studio-tool provider. |
| `AutoOpen` | Optional per-package override for the global `AutoOpenOnPurchase`. |

The grant command accepts the config key, Tebex package ID, Tebex package name, or label. Using stable config keys in Tebex actions is easiest to maintain.

---

## 3. Configure Usage Rules

```lua
Usage = {
    RequireRemainingLiveriesToClaim = true,
},
```

When `true`, a pass with zero livery prints left cannot be reopened only to spend leftover AI generations. A pass with neither liveries nor AI generations remaining can never be claimed again either way.

---

## 4. Add the Tebex Package Command

Add this console command to each Tebex package action:

```text
gsvd_tebex_grant <packageRef> <playerSourceOrIdentifier> <transactionId>
```

Example actions:

```text
gsvd_tebex_grant starter {PLAYER_PLACEHOLDER} {TRANSACTION_PLACEHOLDER}
gsvd_tebex_grant premium {PLAYER_PLACEHOLDER} {TRANSACTION_PLACEHOLDER}
```

Replace the placeholders with the player and transaction variables used by your Tebex and FiveM checkout setup. The player value must resolve to either:

- an online FiveM server ID, or
- a persistent identifier the resource also sees when that player joins, such as a license or citizen identifier.

The transaction value should be unique per purchase. A repeat of the same non-empty transaction ID for the same package returns the existing entitlement instead of granting a second one, which prevents duplicate delivery when Tebex retries a command.

{% hint style="warning" %}
An offline entitlement can only be found later when the identifier passed by Tebex exactly matches one of the identifiers the resource sees for that player. Test this before putting a package on sale.
{% endhint %}

---

## 5. Test from the Server Console

Test with an online source:

```text
gsvd_tebex_grant starter 12 test-transaction-001
```

Test an offline identifier:

```text
gsvd_tebex_grant premium license:abc123 test-transaction-002
```

Expected behavior:

- With `AutoOpenOnPurchase = true`, the studio opens for an online target.
- Otherwise the player is told to use the claim command.
- Offline grants remain in `gs_vehicledesigner_tebex_entitlements` until the matching player claims them.

A staff member running the grant command in game, rather than from the console, needs an ACE:

```cfg
add_ace group.admin gsvd.tebex allow
```

---

## 6. Player Claims

Players open their oldest available pass with:

```text
/gsvd_claim_designer
```

They can request a configured package specifically:

```text
/gsvd_claim_designer starter
```

Opening and closing a pass does not reset its totals. The next claim continues with the remaining database allowance.

---

## 7. Inside a Limited Session

A pass grants a limited session rather than full studio access:

- The player may only edit designs created during that session.
- Printing consumes one livery allowance; reprinting the same design does not consume another.
- AI generations consume the separate AI allowance and can be refunded by the provider when generation fails.
- Players with normal full access do not need a pass. If they claim one anyway, the public state reports that pass and successful prints can still consume its allowance; full access bypasses the normal design and AI permission checks.

Closing the studio does not end the server-side session. It lasts until disconnect, resource restart, or another claim replaces it. Each new claim resets the session's list of created and printed designs, while the remaining allowance stays in the database. Designs from an earlier session cannot be edited with a pass alone.

Station gating still applies to automatic opening and claims. A pass can become active even when the player is too far from a required station to open the studio. Move to a station and use the normal designer command.

Local image upload and asset-list refresh require full access. URL imports accept an active pass when `AllowAssetUrlImports` is enabled.

---

## 8. Commands and Permissions

| Command | Purpose | Access |
|---|---|---|
| `gsvd_tebex_grant <package> <target> <transaction>` | Create a package entitlement | Console, or `gsvd.tebex` in game |
| `gsvd_claim_designer [package]` | Open the player's next claimable entitlement | Player |

Both command names are configurable in `shared/tebex.lua`.

---

## 9. Tebex Exports

```lua
exports['gs-vehicledesigner']:grantTebexPackage(packageRef, target, transactionId)
exports['gs-vehicledesigner']:claimTebexPackage(source, packageRef)
exports['gs-vehicledesigner']:getTebexPackageForPlayer(source, packageRef)
```

See [API & Exports](exports.md#tebex-exports) for return values and examples.

---

## 10. Database

The resource creates this table automatically:

```text
gs_vehicledesigner_tebex_entitlements
```

It stores package totals, remaining livery and AI uses, owner identifiers, state, timestamps, and transaction IDs. Do not decrement it manually; use the resource flows or exports so session state, refunds, and audit records stay synchronized.

---

## Checklist

1. Set `Enabled = true`.
2. Add a package entry with the correct Tebex ID and allowances.
3. Configure the package action with the grant command.
4. Pass a tested player identifier and a unique transaction value.
5. Restart `gs-vehicledesigner` after changing `shared/tebex.lua`.
6. Grant a test package from the server console.
7. Verify online auto-open and offline claiming.
8. Print until the livery allowance reaches zero.
9. Confirm the pass can no longer be claimed once liveries run out.
