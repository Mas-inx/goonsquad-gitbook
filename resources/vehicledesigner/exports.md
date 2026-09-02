# API & Exports

GS Vehicle Designer exposes client and server exports for job menus, paid access, AI providers, pack exports, and administration.

---

## Client Exports

### `openDesigner()`

Opens the studio for the local player.

```lua
local opened = exports['gs-vehicledesigner']:openDesigner()
```

Returns `true` when the studio opened. This does not bypass access control: station gating is checked locally, and the server still validates access before returning the bootstrap payload.

### `closeDesigner()`

Closes the studio and releases NUI focus.

```lua
exports['gs-vehicledesigner']:closeDesigner()
```

### `addImageLayer(payload)`

Adds an image to an already-open studio as a normal editable layer.

```lua
local ok, reason = exports['gs-vehicledesigner']:addImageLayer({
    imageUrl = 'https://cdn.example.com/livery.png',
    fitToCanvas = true,
})
```

Accepted image keys are `imageData`, `imageUrl`, or `url`. It returns `false, 'editor_closed'` when the studio is not open, and `false, 'missing_image'` when no usable image key is supplied.

The same action is available as an event:

```lua
TriggerClientEvent('gs-vehicledesigner:client:addImageLayer', source, payload)
```

### `getStudioContext(timeoutMs)`

Reads the current editor state for an integration. The default timeout is 5000 ms.

```lua
local response = exports['gs-vehicledesigner']:getStudioContext(5000)
if response.ok then
    print(response.context.designName)
end
```

The context contains:

```lua
{
    vehicle = { ... },   -- the selected vehicle entry, or nil
    designId = 42,
    designName = 'Midnight Stripes',
    layers = { ... },
    previewData = 'data:image/png;base64,...',
    canvasWidth = 1024,
    canvasHeight = 1024,
}
```

A closed studio returns `{ ok = false, message = 'The design studio is not open.' }`.

### `useLiveryItem(data, item)`

The `ox_inventory` client export declared in the item definition. It resolves the target vehicle from the item metadata, consumes the item through ox, and fits the livery. It is called by `ox_inventory`, not by integration code.

---

## Server Exports

### `getDesignerAccessState(source)`

Returns how the server currently sees a player's access.

```lua
local state = exports['gs-vehicledesigner']:getDesignerAccessState(source)
```

A full-access player returns:

```lua
{ mode = 'full', limited = false }
```

A player with no access returns `mode = 'none'`. An active pass returns:

```lua
{
    mode = 'limited',
    limited = true,
    sourceType = 'tebex',
    packageKey = 'starter',
    packageLabel = 'Starter Livery Pass',
    liveryLimit = 5,
    liveriesUsed = 2,
    liveriesRemaining = 3,
    aiGenerationLimit = 15,
    aiGenerationsUsed = 4,
    aiGenerationsRemaining = 11,
}
```

### `ExportDesignPack(designId, options)`

Builds or extends a standalone livery pack under `output/`. The pack works without `gs-vehicledesigner` installed.

```lua
local result = exports['gs-vehicledesigner']:ExportDesignPack(42, {
    slotIndex = 3,
    exportMode = 'new',   -- 'new' or 'update'
    version = 2,          -- optional; defaults to the design's current version
})
```

| Option | Description |
|---|---|
| `slotIndex` | Native livery slot to bake into. Auto-allocated from slot 2 upward when omitted, preserving the transparent default. |
| `exportMode` | `new` starts a fresh pack, `update` extends the most recent one. |
| `version` | Export a specific stored version instead of the current one. |

On success the result carries `resourceName`, `model`, `slotIndex`, and `liveryIndex`. On failure it returns `{ ok = false, message = ... }`.

{% hint style="warning" %}
An exported pack streams the same model as the designer's own generated pack. Do not ensure both for the same vehicle at the same time.
{% endhint %}

### `BakeDesignSlot(designId, slotIndex, options)`

Bakes a design permanently into this resource's own streamed files. It takes effect after the next server restart and has zero runtime cost afterwards.

```lua
local result = exports['gs-vehicledesigner']:BakeDesignSlot(42, 17)
```

Valid slots are 1 to 17. Slot 17 is reserved for permanent bakes and is never allocated to a live design.

### `ListExportPacks()`

Lists the packs already built under `output/`, with the vehicles and design entries in each.

```lua
local result = exports['gs-vehicledesigner']:ListExportPacks()
```

### `uploadGeneratedMediaFile(fileName, mimeType, name, purpose)`

Hosts an image written by the AI companion resource with the configured media provider.

```lua
local result = exports['gs-vehicledesigner']:uploadGeneratedMediaFile(
    'gsvd_ai_result_01.png', 'image/png', 'Generated livery', 'ai_generation'
)
```

This export is deliberately restricted. It only accepts calls from a resource named `gsvd-ai`, only reads from that resource's `generated_media/` folder, and only accepts file names matching `gsvd_ai_*`. It fails with a configuration message when the media provider is `database`, because no public URL can be produced.

---

## Studio Tool and AI Integration

GS Vehicle Designer does not call an AI API. A companion resource registers a client studio tool, handles generation, and returns an image.

### Register a Tool

```lua
local ok, reason = exports['gs-vehicledesigner']:registerStudioTool({
    id = 'my_ai',
    eventName = 'my-ai:client:generate',
    label = 'AI',
    panelTitle = 'Generate Livery',
    icon = 'sparkles',
    placeholder = 'Describe the vehicle graphic to create...',
    buttonLabel = 'Generate',
    requireTemplate = true,
    missingTemplateMessage = 'Choose a vehicle first.',
    resourceName = GetCurrentResourceName(),
    timeoutMs = 270000,
})
```

`id` and `eventName` are required; everything else has a default. Supplying `resourceName` lets the studio remove the tool automatically when its provider stops. `timeoutMs` defaults to 270000.

The provider receives its configured event with:

```lua
{
    requestId = '...',
    toolId = 'my_ai',
    prompt = '...',
    context = { ... },   -- the studio context described above
}
```

Return the result to the requesting client:

```lua
TriggerClientEvent('gs-vehicledesigner:client:studioToolResult', source, requestId, {
    ok = true,
    imageUrl = 'https://cdn.example.com/generated.png',
    fitToCanvas = true,
})
```

On failure, return `ok = false` with a player-readable `message`.

### Unregister a Tool

```lua
exports['gs-vehicledesigner']:unregisterStudioTool('my_ai')
```

### Consume and Refund AI Allowance

Before starting billable generation, the provider's server code should consume an allowance:

```lua
local ok, reason, access = exports['gs-vehicledesigner']:consumeAiGeneration(source)
if not ok then
    return false, reason
end
```

If generation fails before a usable image is produced, refund it:

```lua
local refunded, access = exports['gs-vehicledesigner']:refundAiGeneration(source)
```

Full-access players are allowed without a counter. Limited pass sessions enforce the AI limit and decrement the persistent entitlement. Possible failure reasons are `not_allowed` and `ai_limit_reached`.

---

## Tebex Exports

### `grantTebexPackage(packageRef, target, transactionId)`

```lua
local ok, entitlement = exports['gs-vehicledesigner']:grantTebexPackage(
    'starter', source, 'transaction-123'
)
```

`packageRef` may match the config key, Tebex package ID, package name, or label. `target` may be an online server ID or a stored identifier. Reusing the same non-empty transaction ID for the same package returns the existing entitlement instead of granting it twice.

### `claimTebexPackage(source, packageRef)`

```lua
local ok, entitlement = exports['gs-vehicledesigner']:claimTebexPackage(source, 'starter')
```

The package reference is optional. Without one, the oldest claimable entitlement is opened. Failure reasons include `tebex_disabled`, `no_pending_package`, `package_not_found`, and `package_used`.

### `getTebexPackageForPlayer(source, packageRef)`

```lua
local entitlement, reason = exports['gs-vehicledesigner']:getTebexPackageForPlayer(source)
```

Returns the next claimable entitlement for the online player's known identifiers, or `nil` and a reason.

See [Tebex Integration](tebex.md) for package configuration and command setup.

---

## Vehicle Pool Exports

These are registered by the Node runtime and are mainly used internally. The read-only ones are useful for admin panels and monitoring.

| Export | Description |
|---|---|
| `gsvdVehicleSlotStatus()` | Per-model slot totals, free counts, and lock state |
| `gsvdVehiclePoolBootState()` | Current boot and rebuild state of the pool |
| `gsvdRescanVehicles()` | Rescan `vehicle_templates/` and sync the catalog |
| `gsvdRebuildVehiclePool()` | Rescan and run a full materialisation pass |
| `gsvdAllocateVehicleSlot(model)` | Claim the first free live slot for a model |
| `gsvdReleaseVehicleSlot(model, slotIndex)` | Release a slot |

```lua
local status = exports['gs-vehicledesigner']:gsvdVehicleSlotStatus()
```

{% hint style="danger" %}
Do not call the allocate and release exports directly to manage designs. Publishing and deleting already keep the bitmap, the designs table, and the printed items consistent. Manual allocation can strand a slot.
{% endhint %}

---

## Commands and Permissions

| Command | Permission |
|---|---|
| `gsvd_rescan` | `command.gsvd_rescan` |
| `gsvd_rebuild_pool` | `command.gsvd_rebuild_pool` |
| `gsvd_pool_status` | `command.gsvd_pool_status` |
| `gsvd_expand` | `command.gsvd_expand` |
| `gsvd_fill_slots` | `command.gsvd_fill_slots`; development only |
| `gsvd:exportpack` | Admin ACE, or the server console |
| `gsvd:bakeslot` | Admin ACE, or the server console |
| `gsvd:exportlist` | Admin ACE, or the server console |
| `gsvd_whoami` | Server console only |
| Configured Tebex grant command | Console, or `gsvd.tebex` in game |

---

## Database Integration

External admin panels should treat these tables as resource-owned. The most useful read-only tables are:

| Table | Purpose |
|---|---|
| `gs_vehicledesigner_designs` | Drafts, published designs, approval state, slot assignment, and previews |
| `gs_vehicledesigner_design_versions` | Immutable per-version snapshots used by printed items and restore |
| `gs_vehicledesigner_assets` | Uploaded and imported images |
| `gs_vehicledesigner_vehicle_liveries` | Liveries fitted to owned vehicles, keyed by vehicle and plate |
| `gs_vehicledesigner_pool_vehicles` | Discovered vehicles, content hashes, and the live slot bitmap |
| `gs_vehicledesigner_tebex_entitlements` | Package totals, remaining uses, transaction IDs, and state |
| `gs_vehicledesigner_audit` | Design, package, approval, installation, and administrative actions |

Use exports for mutations so slot allocation, inventory metadata, runtime state, refunds, and audit entries stay consistent.
