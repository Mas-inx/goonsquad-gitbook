# Tebex Integration

Tebex packages can grant persistent, limited designer passes. Each package defines how many clothing pieces a player may create and how many AI or studio-tool generations they may use.

---

## How It Works

1. Tebex runs a console command after a purchase.
2. Cloth Designer creates an entitlement in MySQL using the package limits from `shared/tebex.lua`.
3. An online buyer can be taken directly into the designer.
4. An offline buyer claims the entitlement after joining.
5. Clothing and AI usage update the persistent entitlement.

With the recommended rules, clothing allowance is consumed when the final item is printed or approved. AI allowance is consumed by the registered AI provider when generation starts. Once clothing allowance is exhausted, the pass cannot be reclaimed and AI is blocked even if AI uses remain.

---

## 1. Enable Tebex

Open `shared/tebex.lua` and set:

```lua
GSCD.TebexConfig = {
    Enabled = true,
    ClaimCommand = 'gscd_claim_designer',
    GrantCommand = 'gscd_tebex_grant',
    AutoOpenOnPurchase = true,
    NotifyPendingOnJoin = true,
}
```

| Setting | Description |
|---|---|
| `Enabled` | Master switch for package grants and claims. |
| `ClaimCommand` | Player command for opening the next available entitlement. |
| `GrantCommand` | Console command used in Tebex package actions. |
| `AutoOpenOnPurchase` | Opens the pass immediately when the buyer is online. |
| `NotifyPendingOnJoin` | Notifies a player after framework load when a package is waiting. |

---

## 2. Create Package Mappings

Add one config entry for every Tebex package:

```lua
Packages = {
    starter = {
        Label = 'Starter Designer Pass',
        TebexPackageId = '1234567',
        TebexPackageName = 'Starter Designer Pass',
        ComponentAmount = 1,
        AIGenerations = 3,
    },

    premium = {
        Label = 'Premium Designer Pass',
        TebexPackageId = '7654321',
        TebexPackageName = 'Premium Designer Pass',
        ComponentAmount = 5,
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
| `ComponentAmount` | Clothing pieces that can be saved or printed, according to the usage rule. |
| `AIGenerations` | Generations available to a registered AI or studio-tool provider. |
| `AutoOpen` | Optional per-package override for the global `AutoOpenOnPurchase`. |

The grant command accepts the config key, Tebex package ID, Tebex package name, or label. Using stable config keys in Tebex actions is easiest to maintain.

---

## 3. Configure Usage Rules

```lua
Usage = {
    ConsumeComponentOn = 'print',
    RequireRemainingComponentsToClaim = true,
    RequireRemainingComponentsForAI = true,
}
```

### `ConsumeComponentOn`

- `print`: consumes one clothing use when printing succeeds. With approval enabled, approval performs the consumption.
- `save`: consumes one clothing use when a new design is saved.

`print` is recommended because experimenting with or saving a draft does not spend the clothing allowance.

### `RequireRemainingComponentsToClaim`

When `true`, a pass with zero clothing uses cannot be reopened only to spend leftover AI generations.

### `RequireRemainingComponentsForAI`

When `true`, an active pass cannot use AI after its clothing allowance reaches zero, even when AI generations remain.

---

## 4. Add the Tebex Package Command

Add this server or console command to each Tebex package action:

```text
gscd_tebex_grant <packageRef> <playerSourceOrIdentifier> <transactionId>
```

Example actions:

```text
gscd_tebex_grant starter {PLAYER_PLACEHOLDER} {TRANSACTION_PLACEHOLDER}
gscd_tebex_grant premium {PLAYER_PLACEHOLDER} {TRANSACTION_PLACEHOLDER}
```

Replace the placeholders with the player and transaction variables used by your Tebex/FiveM checkout setup. The player value must resolve to either:

- An online FiveM server ID, or
- The same persistent framework identifier Cloth Designer sees when that player joins, such as a license or citizen identifier.

The transaction value should be unique per purchase. Cloth Designer treats the same non-empty transaction ID plus package key as an existing grant, which prevents duplicate delivery when Tebex retries a command.

{% hint style="warning" %}
An offline entitlement can only be found later when the identifier passed by Tebex exactly matches one of the identifiers Cloth Designer sees for that player. Test this before putting a package on sale.
{% endhint %}

---

## 5. Test from the Server Console

Test with an online source:

```text
gscd_tebex_grant starter 12 test-transaction-001
```

Test an offline identifier:

```text
gscd_tebex_grant premium license:abc123 test-transaction-002
```

Expected behavior:

- With `AutoOpenOnPurchase = true`, the designer opens for an online target.
- Otherwise, the player receives a claim notification.
- Offline grants remain in `gs_clothdesigner_tebex_entitlements` until the matching player claims them.

---

## 6. Player Claims

Players open their oldest available pass with:

```text
/gscd_claim_designer
```

They can request a configured package specifically:

```text
/gscd_claim_designer starter
```

Opening and closing a pass does not reset its totals. The next claim continues with the remaining database allowance.

---

## 7. Approval Mode

Tebex passes work with the wardrobe approval queue:

```lua
Wardrobe = {
    Enabled = true,
    Command = 'gscd_wardrobe',
    AdminCommand = 'gscd_clothing_review',
    AdminAce = 'gscd.clothdesigner.admin',
    RequireApproval = true,
}
```

When a limited design is approved:

1. The design is published.
2. One clothing use is consumed when `ConsumeComponentOn = 'print'`.
3. Cloth Designer attempts to add the inventory item.
4. If the creator is offline, the approved design remains available in `/gscd_wardrobe` even though item delivery could not complete.

Grant the review ACE:

```cfg
add_ace group.admin gscd.clothdesigner.admin allow
```

---

## 8. Commands and Permissions

The Tebex server console can run the grant command without an ACE. A player or staff member running it in game needs:

```cfg
add_ace group.admin gscd.tebex allow
```

| Command | Purpose |
|---|---|
| `gscd_tebex_grant <package> <target> <transaction>` | Create a package entitlement. |
| `gscd_claim_designer [package]` | Open the player's next claimable entitlement. |

Both command names are configurable in `shared/tebex.lua`.

---

## 9. Tebex Exports

```lua
exports['gs-clothdesigner']:grantTebexPackage(packageRef, target, transactionId)
exports['gs-clothdesigner']:claimTebexPackage(source, packageRef)
exports['gs-clothdesigner']:getTebexPackageForPlayer(source, packageRef)
```

See [API & Exports](exports.md#tebex-exports) for return values and examples.

---

## 10. Database

The resource creates this table automatically:

```text
gs_clothdesigner_tebex_entitlements
```

It stores package totals, remaining clothing and AI uses, target identifiers, state, timestamps, and transaction IDs. Do not decrement it manually; use the resource flows or exports so session state, refunds, and audits stay synchronized.

---

## Checklist

1. Set `Enabled = true`.
2. Add a package entry with the correct Tebex ID and allowances.
3. Configure the package action with the grant command.
4. Pass a tested player identifier and unique transaction value.
5. Restart `gs-clothdesigner` after changing `shared/tebex.lua`.
6. Grant a test package from the server console.
7. Verify online auto-open and offline claiming.
8. Test printing until the clothing allowance reaches zero.
9. Verify AI is blocked at zero clothing when that rule is enabled.
10. Test admin approval if the server uses it.
