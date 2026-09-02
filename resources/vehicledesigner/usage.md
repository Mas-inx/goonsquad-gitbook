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

If `Designer.requireStation` is enabled, the studio only opens near a configured station. Markers are drawn within `drawDistance`, and pressing **E** inside `interactDistance` opens it.

The studio picks up the vehicle the player is sitting in, or the closest vehicle within roughly 6 metres. That vehicle is preselected in the library. The studio still opens with no vehicle nearby; the player just picks one from the catalog instead.

{% hint style="info" %}
Only vehicles that were converted into the pool can be selected. A car that is streamed by another resource but never placed in `vehicle_templates/` will not appear. See [Adding Vehicles](vehicles.md).
{% endhint %}

---

## The Library

The left pane has two tabs, plus a third for admins:

| Tab | Contents |
|---|---|
| **Vehicles** | The converted vehicle catalog, with search and class filters |
| **My Designs** | The player's own drafts and published finishes |
| **Review** | Pending designs awaiting approval (admins only) |

Each vehicle card is rendered from that vehicle's own game files and shows its live slot occupancy. A card locks when the vehicle and every clone in its series are full. See [Slot Capacity](configuration.md#slot-capacity).

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

The studio accepts PNG, JPEG, WebP, and GIF. Animated GIFs play on the runtime texture when `Runtime.AllowAnimatedGifs` is enabled; the editor canvas shows their first frame.

Files above `MaxUploadBytes` are rejected. HTTPS URL imports are validated server-side, and private, loopback, and link-local addresses are refused.

Uploads, URL imports, and render hosting share the `upload` queue. A full queue returns the configured busy message rather than starting more expensive work.

### AI and Other Studio Tools

An AI tool appears in the toolbar only when a companion resource registers one. A generation returns an ordinary editable image layer that can be moved, resized, or deleted like any other.

In a limited pass session, each successful generation consumes one AI allowance, and a failed generation can be refunded by the provider.

---

## Save and Print

**Save** stores a draft. Drafts are unlimited and can be reopened and edited freely.

**Print Livery** publishes the design, claims a live slot for that vehicle model, captures a render of the vehicle wearing the finish, and adds the inventory item.

Every publish also writes an immutable version snapshot. Older versions can be previewed and restored from the design's history.

{% hint style="info" %}
Printed items are immutable. Editing the source design afterwards never changes an already printed item or a vehicle that has the livery fitted. Fitting an item always applies the exact version that was printed.
{% endhint %}

If the vehicle has no free slot, printing fails with a slot message. Deleting an unused published design frees its slot immediately, with no restart.

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
| Owned (in the framework's vehicle table) | The livery is stored and restored across restarts, garages, and impounds |
| Not owned | The livery lasts until the entity is gone |

Finishes are visible to other players within `Runtime.renderDistance`.

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
- Printing consumes one livery allowance. Reprinting the same design does not consume a second.
- AI generations consume the separate AI allowance.

Full-access players are never limited and have no counters. See [Tebex Integration](tebex.md).

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
| `gsvd_tebex_grant <package> <target> <transaction>` | Grant a configured Tebex pass | Console or Tebex |
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
