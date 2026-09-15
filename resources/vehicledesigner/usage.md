# Usage

This page covers the player, designer, and admin workflows.

---

## Open the Studio

Players open the studio with the command, an alias, or the keybind:

```text
/vehicledesigner
/gsvd
```

The default keybind is **F6**. It is registered through FiveM's key mapping system, so players can rebind it in the game's keyboard settings.

If `Designer.requireStation` is enabled and stations are configured, the studio only opens near one of them. Markers are drawn within `drawDistance`, and pressing **E** inside `interactDistance` opens it.

The studio opens with no vehicle selected. Pick one from the catalog; no nearby vehicle is required. Nearby-vehicle details are sent during bootstrap but are not used to preselect a library card in the current UI.

{% hint style="info" %}
Only vehicles that were converted into the pool can be selected. A car that is streamed by another resource but never placed in `vehicle_templates/` will not appear. See [Adding Vehicles](vehicles.md).
{% endhint %}

---

## The Library

The left pane has two tabs, plus a third for admins:

| Tab | Contents |
|---|---|
| **Vehicles** | The converted vehicle catalog, with search and class filters |
| **My Designs** | The player's 100 most recently updated, non-archived designs |
| **Review** | Pending designs awaiting approval (admins only) |

Each vehicle card is rendered from that vehicle's own game files and shows its slot occupancy at bootstrap. **SLOTS FULL** means that model's own slots are exhausted; a clone may still have room. The card remains selectable for drafting, but a new print needs a free slot. Reopen the studio to refresh occupancy. See [Slot Capacity](configuration.md#slot-capacity).

---

## Create a Design

The editor has two surfaces, switched at the top of the canvas pane:

### 3D Model

Paint directly onto the vehicle. Brush and eraser input raycasts the visible model, interpolates the hit triangle's UV coordinates, and writes ordinary editable stroke layers into the texture.

Non-livery meshes such as glass and interior parts block painting, and strokes that cross a UV seam are split so a brush drag cannot draw an accidental line across the far side of the texture.

### UV Layout

The flat 1024x1024 canvas with the complete 2D toolset: brush, eraser, fill, pen, clone, and smudge, plus editable text, vector shapes, gradients, layer effects, and precise transforms.

Both surfaces edit the same layer document, so you can switch between them at any point in a design.

### Layers and Assets

- Reorder, lock, hide, duplicate, and transform layers from the layer panel
- Undo and redo cover the full editing history
- Uploaded images are kept in a per-player asset library and can be reused across designs
- Custom fonts can be loaded for text layers
- Finishing filters apply to the composed result

### Image Uploads and Imports

The studio accepts PNG, JPEG, WebP, and GIF. Animated GIFs play on the runtime texture when `Runtime.AllowAnimatedGifs` is enabled; the editor canvas shows their first frame. When an animated layer exists, the first animated image is used as the entire runtime texture. Other layers, transforms, and effects are not composited into that animation.

Asset sizes are checked server-side. HTTP and HTTPS URL imports also undergo hostname checks and do not follow redirects. See [Uploads and Media](configuration.md#uploads-and-media) for the checks and their limits.

Hosted-media requests share the `upload` queue; URL downloads and database storage do not. A full hosting queue returns the configured busy message, with database fallback for editable assets.

Local image upload and asset-list refresh require full framework/ACE/job access. A pass alone can use the URL-import route when it is enabled, but does not grant the local-upload permission.

### AI and Other Studio Tools

An AI tool appears in the toolbar only when a companion resource registers one. A generation returns an ordinary editable image layer that can be moved, resized, or deleted like any other.

In a limited pass session, each successful generation consumes one AI allowance, and a failed generation can be refunded by the provider.

---

## Save and Print

**Save** stores the layered document and a new version snapshot. A new design starts as a draft; saving an existing published design keeps its published status. Draft storage is unlimited, while limited passes can edit only designs created in their current session.

**Print Livery** publishes the design, claims a live slot for that vehicle model, captures a render of the vehicle wearing the finish, and adds the inventory item.

Every print attempt saves a version snapshot before publication and inventory checks. Older versions can be loaded from the design's history and saved as a new version.

{% hint style="info" %}
Printed items store their exact design version, and fitting requests that saved version. Different versions of the same design still share one model/slot texture replacement, so nearby vehicles using different versions can overwrite each other's visible finish. Use separate designs, each with its own slot, when both looks need to appear at the same time.
{% endhint %}

If the vehicle has no free slot, printing fails with a slot message. Deleting an unused published design frees its slot immediately, with no restart.

Deletion archives the design and is blocked while an owned-vehicle installation record still references it. There is no built-in remove-livery action. Items for an archived design can no longer be fitted, even though their version metadata is retained.

---

## Fit a Livery to a Vehicle

1. Stand next to the vehicle, or sit in it.
2. Use the printed `gs_vehicle_livery` item from the inventory.

The item is locked to the model it was printed for. Using it near a different vehicle reports which vehicle it fits and does nothing.

The target vehicle must be networked. With `Installation.requirePlayerOwnedVehicle` enabled, its plate must also exist in the framework's owned-vehicle table.

The item is consumed on a successful fit unless `Inventory.consumeOnApply` is set to `false`.

### Persistence

| Vehicle | Behavior |
|---|---|
| Owned (in the framework's vehicle table) | The livery is stored and restored for existing vehicles at startup and through the bundled garage hook |
| Not owned | The livery lasts until the entity is gone |

QBCore/Qbox ownership uses `player_vehicles`; ESX uses `owned_vehicles`. The bundled respawn hook listens to `qbx_garages:server:vehicleSpawned`. For other garages or impounds, adapt the open `server/vehicle_liveries.lua` bridge to call `GSVD.VehicleLiveries.RestoreEntity(entity)` after the vehicle and its plate are ready.

The periodic runtime scan prepares finishes within `Runtime.renderDistance`. State-bag updates can also apply them, and textures already loaded remain active until released or the resource stops.

---

## Approval Workflow

Set `Approval.enabled = true` to review finishes before they are published.

### Player Flow

1. The player finishes a design and selects **Print Livery**.
2. The design is submitted instead of published; no slot is claimed and no item is granted.
3. Once an admin approves it, the player prints again and the design publishes normally.
4. A rejection records the reviewer's note and returns the design to the player as a draft.

### Admin Flow

Admins see a **Review** tab in the library, listing the oldest pending designs first. Each can be previewed, approved, or rejected with a note. Admins bypass the queue for their own designs.

Admin status comes from the `gs_vehicledesigner.admin` ACE, or from the global `command` ACE:

```cfg
add_ace group.admin gs_vehicledesigner.admin allow
```

{% hint style="warning" %}
Approval is recorded on the design, not on the version. Once a design has been approved, later edits to it publish without returning to the queue. If you need every revision reviewed, ask designers to submit changes as a new design.
{% endhint %}

---

## Designer Passes

When Tebex integration is enabled, a package grants a **limited** session with its own livery-print and AI-generation allowances, tracked persistently in the database.

An online buyer is taken straight into the studio when `AutoOpenOnPurchase` is enabled. An offline buyer claims their pass after joining:

```text
/gsvd_claim_designer
```

Within a limited session:

- A player may only edit designs created during that session.
- Printing consumes one livery allowance. Reprinting the same design during that session does not consume a second.
- AI generations consume the separate AI allowance.

Closing the studio keeps the pass session active. Claiming again starts a fresh session; disconnecting or restarting the resource clears it. Full-access players normally use the studio without a pass. See [Tebex Integration](tebex.md).

---

## Standalone Pack Export

Admins see an **Export Pack** button in the studio. It bakes a saved design into a standalone resource under `output/`, which works without `gs-vehicledesigner` installed.

The same operation is available from the console:

```text
gsvd:exportpack <designId> [slotIndex] [new|update]
```

{% hint style="warning" %}
An exported pack streams the same vehicle model as the designer's own generated pack. Do not run both for the same model at the same time.
{% endhint %}

A design can also be baked permanently into this resource's own streamed files, which takes effect after the next server restart and costs nothing at runtime:

```text
gsvd:bakeslot <designId> <slotIndex 1-17>
```

Slot 17 is reserved for these permanent bakes, which is why 16 live slots remain available.

---

## Commands

| Command | Purpose | Access |
|---|---|---|
| `/vehicledesigner`, `/gsvd` | Open the studio | Player |
| `/gsvd_claim_designer [package]` | Claim the next or named Tebex pass | Player |
| `gsvd_tebex_grant <package> <target> <transaction>` | Grant a configured Tebex pass | Console, or `gsvd.tebex` in game |
| `gsvd_whoami <serverId>` | Print how the designer sees a player's permissions | Server console only |
| `gsvd_rescan` | Rescan vehicle templates | Restricted command ACE |
| `gsvd_rebuild_pool` | Rescan and rebuild the vehicle pool | Restricted command ACE |
| `gsvd_pool_status` | Print per-model slot occupancy | Restricted command ACE |
| `gsvd_expand <model>` | Force-mint the next series clone | Restricted command ACE |
| `gsvd_fill_slots <model>` | Occupy every slot for a model; development only | Restricted command ACE |
| `gsvd:exportpack <designId> [slot] [new\|update]` | Build a standalone livery pack | Admin or console |
| `gsvd:bakeslot <designId> <slot>` | Bake a design into this resource's stream | Admin or console |
| `gsvd:exportlist` | List existing export packs | Admin or console |

See [Adding Vehicles](vehicles.md#console-commands) for the pool commands in context.
