# API & Exports

GS Cloth Designer exposes client and server exports for job menus, paid access, custom AI providers, wardrobe shortcuts, and administration.

---

## Client Exports

### `openDesigner()`

Opens the normal designer for the local player.

```lua
local opened, result = exports['gs-clothdesigner']:openDesigner()
```

This does not bypass access control. The server still requires a job and grade from `Config.Designer.jobs`, or an active limited-access session.

### Limited Designer Access

`summonDesigner(componentAmount, aiGenAmount)` opens a limited designer session for the local player:

```lua
local opened, result = exports['gs-clothdesigner']:summonDesigner(2, 4)
```

`openLimitedDesigner` is an alias:

```lua
exports['gs-clothdesigner']:openLimitedDesigner(2, 4)
```

| Parameter | Type | Description |
|---|---|---|
| `componentAmount` | number | Clothing pieces the player may create or print, depending on the configured consumption rule. |
| `aiGenAmount` | number | AI or studio-tool generations available to the session. |

Values are rounded down and negative or invalid values become `0`. The session ends when the designer closes or the player disconnects. Use the Tebex integration when allowances must persist in the database.

{% hint style="info" %}
These client exports are suitable when your trusted client flow already received a server-authorized entitlement. For purchases, rewards, or admin grants, call the server export so the server chooses the target and limits.
{% endhint %}

### `openWardrobe()`

Opens the local player's created-clothing wardrobe:

```lua
exports['gs-clothdesigner']:openWardrobe()
```

The wardrobe must be enabled in `Config.Wardrobe`.

### `openClothingReview()`

Opens the admin review UI:

```lua
exports['gs-clothdesigner']:openClothingReview()
```

The server still checks `Config.Wardrobe.AdminAce` before returning pending designs or accepting a review action.

### `addImageLayer(payload)`

Adds an image to an already-open studio:

```lua
local ok, reason = exports['gs-clothdesigner']:addImageLayer({
    imageUrl = 'https://cdn.example.com/design.png',
    fitToCanvas = true,
    placement = 'texture-canvas',
})
```

Accepted image keys are `imageData`, `imageUrl`, or `url`. Optional placement fields are `width`, `height`, `x`, `y`, `fitToCanvas`, and `placement` (`texture-canvas` or `placed`).

### `getStudioContext(timeoutMs)`

Reads the current editor state for an integration:

```lua
local response = exports['gs-clothdesigner']:getStudioContext(5000)
if response.ok then
    local context = response.context
    print(context.designName, context.article)
end
```

The context contains `designName`, `designId`, `article`, `activeTemplateGuideUrl`, `textureDataUrl`, and an `activeModel` table when a template is selected.

---

## Server Exports

### `summonDesigner(target, componentAmount, aiGenAmount)`

Opens a limited designer session for an online player:

```lua
local ok, reason = exports['gs-clothdesigner']:summonDesigner(source, 2, 4)
```

`summonDesignerForPlayer` is an explicit alias with the same parameters:

```lua
exports['gs-clothdesigner']:summonDesignerForPlayer(source, 2, 4)
```

The server form always requires the target player's server ID as the first argument.

### `printShirtItem(source, designId)`

Adds an inventory item for an existing published design:

```lua
local ok, result = exports['gs-clothdesigner']:printShirtItem(source, 42)
```

| Parameter | Type | Description |
|---|---|---|
| `source` | number | Online player's server ID. |
| `designId` | number | Published design ID. |

The call fails when the player is offline, the design is not printable, the item definition is missing, or the inventory rejects the item.

### `deleteDesign(designId, actorIdentifier?, actorName?)`

Deletes a design, releases its generated slot, clears matching equipped state, and writes an audit record:

```lua
local ok, result = exports['gs-clothdesigner']:deleteDesign(
    42,
    'license:admin_identifier',
    'Admin Name'
)
```

It returns `true, result` on success or `false, reason` on failure.

### `getDesignerAccessState(source)`

Returns the player's current access state:

```lua
local state = exports['gs-clothdesigner']:getDesignerAccessState(source)
```

Limited-state fields include:

```lua
{
    mode = 'limited',
    limited = true,
    sourceType = 'tebex',
    entitlementId = 12,
    packageKey = 'starter',
    packageLabel = 'Starter Designer Pass',
    componentLimit = 2,
    componentsUsed = 1,
    componentRemaining = 1,
    aiGenerationLimit = 4,
    aiGenerationsUsed = 2,
    aiGenerationsRemaining = 2,
}
```

A normal job-authorized player returns `mode = 'job'` and `limited = false`. A player without access returns `mode = 'none'`.

---

## Studio Tool and AI Integration

GS Cloth Designer does not call an AI API directly. A companion resource registers a client studio tool, handles generation, and returns an image.

### Register a Tool

```lua
local ok, reason = exports['gs-clothdesigner']:registerStudioTool({
    id = 'my_ai',
    eventName = 'my-ai:client:generate',
    label = 'AI',
    panelTitle = 'Generate Texture',
    icon = 'sparkles',
    placeholder = 'Describe the texture to create...',
    buttonLabel = 'Generate',
    requireTemplate = true,
    missingTemplateMessage = 'Choose a clothing template first.',
    resourceName = GetCurrentResourceName(),
})
```

`id` and `eventName` are required. Supplying `resourceName` lets Cloth Designer remove the tool automatically if its provider stops.

The provider receives its configured event with:

```lua
{
    requestId = '...',
    toolId = 'my_ai',
    prompt = '...',
    context = {
        designName = '...',
        designId = 42,
        article = 'shirt',
        referenceImage = 'data:image/png;base64,...',
        referenceImageUrl = 'https://...',
        textureDataUrl = 'data:image/png;base64,...',
        activeModel = { ... },
    }
}
```

Return the result to the requesting client:

```lua
TriggerEvent('gs-clothdesigner:client:studioToolResult', requestId, {
    ok = true,
    imageUrl = 'https://cdn.example.com/generated.png',
    fitToCanvas = true,
    access = accessState,
})
```

On failure, return `ok = false` and a player-readable `message`.

### Consume and Refund AI Allowance

Before starting billable generation, the provider's server code should consume an allowance:

```lua
local ok, reason, access = exports['gs-clothdesigner']:consumeAiGeneration(source)
if not ok then
    return false, reason, access
end
```

If generation fails before a usable image is returned, refund it:

```lua
local refunded, access = exports['gs-clothdesigner']:refundAiGeneration(source)
```

Job-authorized, non-limited players are allowed without a counter. Limited and Tebex sessions enforce both the AI limit and, when configured, the requirement that clothing uses remain.

### Unregister a Tool

```lua
exports['gs-clothdesigner']:unregisterStudioTool('my_ai')
```

---

## Tebex Exports

### `grantTebexPackage(packageRef, target, transactionId)`

```lua
local ok, entitlement = exports['gs-clothdesigner']:grantTebexPackage(
    'starter',
    source,
    'transaction-123'
)
```

`packageRef` may match the config key, Tebex package ID, package name, or label. `target` may be an online server ID or a stored framework identifier. Reusing the same non-empty transaction ID for the same package returns the existing entitlement instead of granting it twice.

### `claimTebexPackage(source, packageRef?)`

```lua
local ok, entitlement = exports['gs-clothdesigner']:claimTebexPackage(source, 'starter')
```

The package reference is optional. Without one, the oldest claimable entitlement is opened.

### `getTebexPackageForPlayer(source, packageRef?)`

```lua
local entitlement, reason = exports['gs-clothdesigner']:getTebexPackageForPlayer(source)
```

Returns the next claimable entitlement for the online player's known identifiers.

See [Tebex Integration](tebex.md) for package configuration and command setup.

---

## Client Wearable Clear Event

From client code, clear one component for the local player:

```lua
TriggerServerEvent('gs-clothdesigner:server:clearWearableState', {
    drawableType = 'component',
    componentId = 11,
})
```

Clear one prop:

```lua
TriggerServerEvent('gs-clothdesigner:server:clearWearableState', {
    drawableType = 'prop',
    propId = 0,
})
```

Clear all custom wearables and their persisted active rows:

```lua
TriggerServerEvent('gs-clothdesigner:server:clearWearableState', {})
```

The server always uses the sending player's source, so a client cannot clear another player's state by supplying a target ID. Trigger this only from a trusted outfit or character flow; calling it with an empty payload removes every persisted GS Cloth Designer wearable for the local player.

---

## Public Commands

| Command | Permission |
|---|---|
| `gscd_delete_design` | `command.gscd_delete_design` |
| `gscd_rescan` | `command.gscd_rescan` |
| `gscd_rebuild_pool` | `command.gscd_rebuild_pool` |
| `gscd_fill_slots` | `command.gscd_fill_slots`; development only |
| Configured wardrobe admin command | `Config.Wardrobe.AdminAce` |
| Configured Tebex grant command | Console or `gscd.tebex` |

---

## Database Integration

External admin panels should treat Cloth Designer tables as resource-owned. The most useful read-only tables are:

| Table | Purpose |
|---|---|
| `gs_clothdesigner_designs` | Drafts, published designs, approval state, template assignment, and previews |
| `gs_clothdesigner_assets` | Uploaded and imported images |
| `gs_clothdesigner_active_wearables` | Equipped component and prop state plus previous appearance |
| `gs_clothdesigner_tebex_entitlements` | Package totals, remaining uses, transaction IDs, and state |
| `gs_clothdesigner_audit` | Design, package, approval, and administrative actions |

Use exports for mutations so slot allocation, inventory metadata, wearable state, refunds, and audit entries remain consistent.
