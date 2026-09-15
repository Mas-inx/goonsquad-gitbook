# Configuration

## File Layout

GS Vehicle Designer uses three configuration files:

| File | Purpose | Client-readable |
|---|---|---|
| `shared/config.lua` | Main resource, access, media, inventory, runtime, station, and queue settings | Yes |
| `shared/tebex.lua` | Tebex packages and designer-pass usage rules | Yes |
| `server/credentials.lua` | FiveManage token and Discord webhook | No |

These files stay open and editable under FiveM Asset Escrow, together with the bridge files you may need to adapt:

| File | Purpose |
|---|---|
| `server/framework.lua` | Framework detection, identifiers, jobs, admin checks |
| `server/inventory.lua` | Inventory backend detection and item handling |
| `server/vehicle_liveries.lua` | Owned-vehicle lookups for livery persistence |
| `client/notify.lua` | Notification output |
| `install/*.lua` | Item definition snippets |

Vehicle integration assets also stay readable:

| Path | Purpose |
|---|---|
| `vehicle_templates/**/*.lua` | Vehicle display-name lists read during discovery |
| `vehicle_templates/**/*.yft` | Source models read and rewritten during conversion |
| `uv_output/**/*.yft` | Model copies decoded by the browser preview |
| `stream/**/*.yft` | Generated models reused by clones and standalone exports |

The model patterns apply recursively within each listed folder. Keep these exclusions when uploading a build with vehicle assets; the converter and browser preview need readable model bytes. Assets generated after installation remain readable as well.

Other resource Lua scripts are eligible for escrow protection. JavaScript, NUI files, JSON, SQL, and vehicle metadata remain readable because those formats are outside the currently supported [Cfx Asset Escrow formats](https://docs.fivem.net/docs/server-manual/asset-escrow/).

{% hint style="warning" %}
Keep API tokens and webhook URLs in `server/credentials.lua`. Never move secrets into either shared file.
{% endhint %}

---

## General

```lua
Debug = false
Command = 'vehicledesigner'
CommandAliases = { 'gsvd' },
RequireDriverSeat = false
```

| Setting | Description |
|---|---|
| `Debug` | Adds detailed client and server diagnostics. Use it while installing or investigating an issue; disable it for normal production use. |
| `Command` | Command that opens the studio. It is also the name bound to the **F6** keybind, so players can rebind it in FiveM's key settings. |
| `CommandAliases` | Extra commands that open the studio. |
| `RequireDriverSeat` | Filters the nearby-vehicle context sent during bootstrap. When `true`, only the vehicle the player is driving qualifies. The current UI still opens with no vehicle selected, so players choose from the catalog. |

`StateBagKey`, `CallbackEvent`, and `CallbackResponseEvent` are protocol settings. Change them only when another resource conflicts and every integration is updated to match.

---

## Access

```lua
Access = {
    ace = 'gs_vehicledesigner.use',
    allowEveryone = true,
    adminAce = 'gs_vehicledesigner.admin',
    designerJobs = {
        mechanic = 0,
    },
},
```

| Setting | Description |
|---|---|
| `ace` | ACE that grants studio access. |
| `allowEveryone` | When `true`, every player can open the studio. |
| `adminAce` | ACE for approval review, pack exports, and slot bakes. |
| `designerJobs` | Maps a framework job name to its minimum grade. |

{% hint style="warning" %}
`allowEveryone` ships as `true`. Set it to `false` before going live if the studio should be restricted.
{% endhint %}

A player has full access when any of these is true: `allowEveryone` is on, they are an admin, they hold the `ace`, or their job and grade are listed in `designerJobs`. A Tebex designer pass grants a separate **limited** session instead of full access.

Admin status comes from the `adminAce`, and as a fallback from the global `command` ACE, which txAdmin's *Full Permissions* and classic admin setups already grant.

Verify how the resource sees a player from the server console:

```text
gsvd_whoami <serverId>
```

---

## Designer Stations

```lua
Designer = {
    requireStation = false,
    stations = {},
},
```

| Setting | Description |
|---|---|
| `requireStation` | When `true`, the command and keybind only work near a configured station. |
| `stations` | World locations where the studio can be opened. |

With no stations configured, or with `requireStation = false`, the studio opens anywhere. The marker loop stays idle only when the station list is empty; configured stations still draw markers and accept **E** when proximity gating is off.

```lua
stations = {
    {
        label = 'Vehicle Livery Studio',
        coords = vec3(-211.55, -1324.35, 30.89),
        marker = vec3(1.4, 1.4, 0.6),
        interactDistance = 2.0,
        drawDistance = 20.0,
    },
},
```

| Field | Description |
|---|---|
| `label` | Shown in the interaction prompt. |
| `coords` | Station position. |
| `marker` | Marker size as a `vec3`. |
| `interactDistance` | Range at which **E** opens the studio. |
| `drawDistance` | Range at which the marker is drawn. |

---

## Inventory and Installation

```lua
Inventory = {
    itemName = 'gs_vehicle_livery',
    itemLabel = 'Custom Vehicle Livery',
    consumeOnApply = true,
},

Installation = {
    requirePlayerOwnedVehicle = false,
    maxDistance = 5.0,
},
```

| Setting | Description |
|---|---|
| `itemName` | Item added when a livery is printed. It must match the active inventory definition. |
| `itemLabel` | Fallback display label used by integrations and notifications. |
| `consumeOnApply` | Removes the printed item after a successful installation. Set `false` to make liveries reusable. |
| `requirePlayerOwnedVehicle` | When `true`, a livery can only be fitted to a vehicle whose plate exists in the framework's owned-vehicle table. |
| `maxDistance` | How far the player may stand from the target vehicle when using the item. |

{% hint style="info" %}
`requirePlayerOwnedVehicle` does not require the installer to own the car. Any matching owned vehicle qualifies. Ownership uses `player_vehicles` on QBCore/Qbox and `owned_vehicles` on ESX. Standalone installs need a custom ownership bridge to use this requirement. See [Persistence](usage.md#persistence) for garage restoration; unowned vehicles receive a non-persistent finish that lasts until the entity is gone.
{% endhint %}

---

## Slot Capacity

```lua
SlotCapacity = 16
```

Stored drafts are unlimited. The allocator and converter currently use a fixed capacity of **16 live designs per model**. Keep `SlotCapacity = 16`: changing this config value changes reported capacity and messages, but does not resize the allocator or generated texture slots.

The converter registers 17 native livery slots per model. Slot 17 is reserved for permanent bakes made with `gsvd:bakeslot`, which is why the live capacity is 16.

Publishing claims the first free slot for the model; deleting a design releases it. There is no separate unpublish action. Occupancy combines the database bitmap with every design that still has a slot assigned. Manual database changes or development slot reservations can leave bits occupied.

The studio's vehicle cards show per-model occupancy and mark that model **SLOTS FULL** when its own slots are exhausted. When a model and all its existing clones are full, the next rebuild provisions a **series clone**. See [Adding Vehicles](vehicles.md#series-clones).

---

## Uploads and Media

```lua
AllowFileUploads = true
AllowAssetUrlImports = true
AssetUploadProvider = 'discord' -- database | fivemanage | discord
MaxUploadBytes = 8 * 1024 * 1024
SupportedMimeTypes = {
    ['image/png'] = true,
    ['image/jpeg'] = true,
    ['image/webp'] = true,
    ['image/gif'] = true,
}
```

| Setting | Description |
|---|---|
| `AllowFileUploads` | Controls the studio's upload button and image-tool picker. The server upload callback does not independently enforce this flag. |
| `AllowAssetUrlImports` | Allows players to import public HTTP or HTTPS image URLs. |
| `AssetUploadProvider` | `database`, `fivemanage`, or `discord`. Any other value falls back to `database`. |
| `MaxUploadBytes` | Maximum uploaded asset size. |
| `SupportedMimeTypes` | Accepted image types. GIF is accepted for animated layers. |

URL imports accept HTTP and HTTPS. The server rejects embedded credentials, bracketed IPv6 hosts, `localhost`, `127.0.0.1`, selected local hostname suffixes, and common private IPv4 ranges. Redirects are disabled. This is a hostname check, not DNS-resolution filtering, so it is not a complete barrier to internal addresses.

{% hint style="info" %}
When a hosted provider fails, the uploaded image is still stored in the database rather than being discarded. The player's work is never lost because a webhook was rate-limited.
{% endhint %}

Editable asset bytes are retained in the database even when hosting succeeds. Hosted storage supplies a public URL; it does not remove the local copy.

### FiveManage

```lua
FiveManage = {
    base64Endpoint = 'https://api.fivemanage.com/api/v3/file/base64',
    filenamePrefix = 'gsvd_asset',
    metadataName = 'GS Vehicle Designer Asset',
    metadataDescription = 'Uploaded from gs-vehicledesigner',
    path = '',
    retentionExempt = true,
}
```

Set `AssetUploadProvider = 'fivemanage'` and place the token in `server/credentials.lua`:

```lua
GSVD.Credentials.FiveManageApiKey = 'YOUR_API_KEY'
```

Keep the default endpoint unless FiveManage documents a replacement. `path` is an optional folder inside the FiveManage team. Only enable `retentionExempt` when the token and account permit it.

### Discord

```lua
Discord = {
    filenamePrefix = 'gsvd_asset',
}
```

Set `AssetUploadProvider = 'discord'` and configure:

```lua
GSVD.Credentials.DiscordWebhookUrl = 'YOUR_DISCORD_WEBHOOK_URL'
```

Only genuine Discord webhook hosts are accepted. Uploads run in the Node runtime with native `fetch` and `FormData`; large vehicle renders are streamed across the Lua-to-JS boundary in chunks.

### Database

Set `AssetUploadProvider = 'database'`. Assets and previews are stored in MySQL, so monitor database size on an active server. Printed items keep their fallback icon because no public URL exists for the inventory to load.

---

## Queues

```lua
Queues = {
    upload = {
        enabled = true,
        maxConcurrent = 2,
        maxPending = 12,
        busyMessage = 'The server is busy uploading other renders. Please try again in a moment.',
    },
    export = {
        enabled = true,
        maxConcurrent = 1,
        maxPending = 4,
        busyMessage = 'Another livery pack export is still running. Please try again in a moment.',
    },
},
```

| Setting | Description |
|---|---|
| `enabled` | Set `false` to run jobs immediately. Keeping it enabled is recommended. |
| `maxConcurrent` | Jobs allowed to run at the same time. |
| `maxPending` | Waiting jobs accepted before new requests receive `busyMessage`. |
| `busyMessage` | Error shown when the pending queue is full. |

The `upload` queue bounds hosted-media requests for assets, item previews, and generated images. URL downloads and database storage happen outside this queue. The `export` queue bounds pack builds requested through the studio; console commands and direct exports do not enter it. All export and bake operations are still serialized by the server's internal export queue.

---

## Approval

```lua
Approval = {
    enabled = false,
    autoApproveAdmins = true,
},
```

| Setting | Description |
|---|---|
| `enabled` | Sends publish requests to a review queue instead of publishing immediately. |
| `autoApproveAdmins` | Present for clarity. Admins bypass the queue regardless of this value. |

See [Usage](usage.md#approval-workflow) for the full flow.

---

## Runtime Textures

```lua
Runtime = {
    AllowAnimatedGifs = true,
    duiWidth = 1024,
    duiHeight = 1024,
    initialDelayMs = 250,
    retryIntervalMs = 650,
    retryWindowMs = 8000,
    scanIntervalMs = 900,
    renderDistance = 180.0,
    callbackTimeoutMs = 60000,
},
```

| Setting | Description |
|---|---|
| `AllowAnimatedGifs` | Allows animated GIF layers to play on the runtime texture. |
| `duiWidth`, `duiHeight` | Hidden runtime texture canvas resolution. |
| `initialDelayMs` | Wait before binding a newly created DUI texture. |
| `retryIntervalMs` | Delay between binding retries. |
| `retryWindowMs` | Total retry window before the attempt is abandoned. |
| `scanIntervalMs` | Milliseconds between nearby-vehicle scans. |
| `renderDistance` | Distance used by the periodic nearby-vehicle scan. State-bag updates can apply a finish outside that distance, and existing textures are not released just because a vehicle moves away. |
| `callbackTimeoutMs` | Timeout for saves, publishing, large asset/version requests, and runtime image retrieval. |

{% hint style="info" %}
On a high-population server, reduce `renderDistance` before making scans more frequent. Raising the DUI resolution or the render distance increases client memory and CPU use.
{% endhint %}

---

## Limits

```lua
Limits = {
    nameLength = 80,
    maxLayersJsonBytes = 6 * 1024 * 1024,
    maxPreviewBytes = 12 * 1024 * 1024,
},
```

| Setting | Description |
|---|---|
| `nameLength` | Maximum design name length in bytes; longer names are truncated. |
| `maxLayersJsonBytes` | Maximum serialized layer document size. |
| `maxPreviewBytes` | Maximum saved runtime preview data URL length, including its base64 text. The separate inventory vehicle render is checked against `MaxUploadBytes` using its decoded size. |

These limits are enforced server-side when a design is saved or printed.

---

## Tebex Configuration

Package allowances and claiming rules live in `shared/tebex.lua`, not the main config. See [Tebex Integration](tebex.md).

---

## AI and Studio Tools

There is no AI configuration or provider key in GS Vehicle Designer. An AI tool appears only when a companion resource registers a studio tool through `registerStudioTool`. That integration consumes and refunds AI allowances through the server exports documented in [API & Exports](exports.md#studio-tool-and-ai-integration).

The optional `gsvd-ai` companion resource holds its own provider key in its own server-only config file.
