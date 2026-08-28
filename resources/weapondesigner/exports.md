# API & Exports

Integration points for other resources. Anything not listed here — internal events, NUI callbacks, and the generated files — is implementation detail and may change between versions.

---

## Client Exports

### openDesigner()

Opens the full design studio, subject to the same access checks as the command.

```lua
exports['gs-weapondesigner']:openDesigner()
```

### openArmory()

Opens the player's design library.

```lua
exports['gs-weapondesigner']:openArmory()
```

### GiveSkinnedWeapon(weaponName, ammo, equipActive)

Gives the local player a slot weapon and applies its design. Intended for custom shop or reward flows that already know the weapon name.

```lua
exports['gs-weapondesigner']:GiveSkinnedWeapon('WEAPON_ASSAULTRIFLE_GSWD_00', 250, true)
```

| Parameter | Type | Description |
|---|---|---|
| `weaponName` | string or hash | The slot weapon to give |
| `ammo` | number | Ammo to load |
| `equipActive` | boolean | Equip the weapon in hand immediately |

---

## Server Exports

### summonDesigner(target, weaponAmount, aiAmount, options)

Opens a limited designer session for a player with a temporary allowance, independent of Tebex. Useful for event rewards, job perks, or custom monetization.

```lua
exports['gs-weapondesigner']:summonDesigner(source, 1, 5)
```

| Parameter | Type | Description |
|---|---|---|
| `target` | number | Server ID of the player |
| `weaponAmount` | number | Weapon forges to allow in this session |
| `aiAmount` | number | AI generations to allow in this session |
| `options` | table | Optional session options |

### hasDesignerAccess(source)

Returns whether the player currently has designer access (base access, job, or an active pass).

```lua
local allowed = exports['gs-weapondesigner']:hasDesignerAccess(source)
```

### equipDesign(source, designId) / unequipDesign(source) / toggleDesign(source, designId)

Equip or remove a published design on a player. Ownership is verified server-side.

```lua
exports['gs-weapondesigner']:equipDesign(source, designId)
exports['gs-weapondesigner']:unequipDesign(source)
```

### deleteDesign(source, designId)

Deletes a design on behalf of its owner, removing the inventory weapon and freeing its slot.

```lua
exports['gs-weapondesigner']:deleteDesign(source, designId)
```

### printWeaponItem(source, designId)

Re-grants the inventory weapon for a published design the player owns — for example after an inventory wipe.

```lua
exports['gs-weapondesigner']:printWeaponItem(source, designId)
```

### openArmory(target, adminReview)

Opens the armory for a player; pass `true` as the second argument to open the staff review view (ACE-checked).

```lua
exports['gs-weapondesigner']:openArmory(target, false)
```

### Tebex exports

```lua
exports['gs-weapondesigner']:grantTebexPackage(packageRef, target, transactionId)
exports['gs-weapondesigner']:claimTebexPackage(source, packageRef)
```

Programmatic equivalents of the grant and claim commands — see [Tebex Integration](tebex.md).

---

## Studio Tool API

External resources can add their own tool button to the studio's toolbar. This is the same contract the `gswd-ai` addon uses.

### registerStudioTool(tool)

```lua
exports['gs-weapondesigner']:registerStudioTool({
    id = 'my_tool',
    eventName = 'myresource:client:runTool',
    label = 'My Tool',
    panelTitle = 'My Tool',
    buttonLabel = 'Generate',
    placeholder = 'Describe what to create...',
    requireTemplate = true,
    resourceName = GetCurrentResourceName(),
})
```

| Field | Required | Description |
|---|---|---|
| `id` | Yes | Unique tool identifier |
| `eventName` | Yes | Client event fired when the player runs the tool |
| `label` / `panelTitle` / `buttonLabel` / `placeholder` / `icon` | No | UI text for the tool panel |
| `requireTemplate` | No | Only allow the tool once a weapon is selected |
| `resourceName` | No | Lets the tool auto-unregister when your resource stops |
| `timeoutMs` | No | How long the studio waits for a result (default 270000) |

When the event fires it receives a request ID and the studio context. Finish the request with:

```lua
TriggerEvent('gs-weapondesigner:client:studioToolResult', requestId, {
    ok = true,
    imageDataUrl = dataUrl,
})
```

### Related exports

```lua
exports['gs-weapondesigner']:unregisterStudioTool(id)
exports['gs-weapondesigner']:getStudioContext(timeoutMs)
exports['gs-weapondesigner']:addImageLayer(payload)
```

`addImageLayer` inserts an image directly onto the player's active canvas as a new layer.

---

## Client Events

| Event | Direction | Description |
|---|---|---|
| `gs-weapondesigner:client:openLimitedDesigner` | server → client | Opens a limited session (used by the pass system) |
| `gs-weapondesigner:client:openArmory` | server → client | Opens the armory, optionally in review mode |
| `gs-weapondesigner:client:studioToolResult` | client | Completes a studio tool request |

---

## Console Commands

| Command | Description |
|---|---|
| `gswd_pool_status` | Print per-weapon slot occupancy |
| `gswd_rescan` | Re-scan `weapon_templates/` and rebuild the template manifest |
| `gswd_rebuild_pool` | Rebuild every slot pack; restart the resource afterwards if the console says `fxmanifest.lua` changed |

---

## Database Integration

Designs, slots, versions, entitlements, and audit entries live in the `gs_weapondesigner_*` tables listed in [Installation](installation.md#step-3-database-setup-optional). External tools may read them; writing to them directly is not supported.
