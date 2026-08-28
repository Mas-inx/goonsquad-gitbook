# Usage

## Opening the Designer

Players can reach the studio in several ways, depending on configuration:

- The `/gswd` chat command (configurable, can be disabled)
- An optional keybind, rebindable per player in FiveM's key settings
- Pressing E at a configured designer station
- A limited session opened by a Tebex pass or an export from another resource

If job gating is enabled, access is checked when the studio opens and again on every action.

---

## Designing a Weapon

1. **Pick a weapon.** The studio lists every installed weapon template with a 3D preview.
2. **Pick a part.** The body, magazines, suppressor, scope, grip, and flashlight are separate designable parts.
3. **Pick a surface.** Each part exposes its paintable texture surfaces; a colour-coded UV wireframe guide is drawn on the canvas so players can see which region maps to which area of the model.
4. **Design.** The canvas supports painting, erasing, shapes, text, layer management, undo/redo, local image uploads, and HTTPS image imports. The 3D preview updates live.
5. If the AI addon is installed and the player has generations available, an AI tool button appears in the studio.

---

## Saving and Forging

- **Save** stores the design as a draft. Drafts can be reloaded and edited at any time and consume no slot.
- **Forge** publishes the design: it claims a weapon slot, records a version snapshot, uploads a preview image, and grants the weapon to the player's inventory.

Re-forging an already published design updates it in place — the same slot and the same inventory item are reused, and a new version is added to its history. Players never end up with duplicate weapons from editing a design.

{% hint style="info" %}
If every slot for a weapon is occupied, or the server-wide design ceiling is reached, the forge is refused with a message. Slot counts are controlled by `data/weapon_slots.json` — see [Configuration](configuration.md#the-slot-system-dataweapon_slotsjson).
{% endhint %}

---

## The Armory

The `/gswd_armory` command opens the player's design library. From there players can:

- **Load** a design back into the studio for further editing
- **History** — browse every published snapshot and restore any of them; the next save becomes a new revision
- **Equip** a published design directly
- **Delete** a design — deleting a published design removes the matching weapon from the player's inventory, clears it for nearby players, and frees its slot for someone else

---

## Carrying and Equipping

Forged weapons live in the player's inventory:

- On `ox_inventory`, the design is a real weapon item with its own label, preview image, and working attachment components.
- On other inventories, the design is a `gs_customweapon` ticket item; using it equips the custom weapon, using it again unequips.

While a design is equipped, nearby players see the custom skin. Equipped designs are restored automatically after reconnects and server restarts.

---

## Staff Approval

With `Armory.RequireApproval` enabled, forging does not grant a weapon immediately. The design reserves its slot and enters a pending state, and a staff member with the `gswd.weapondesigner.admin` ACE reviews it in `/gswd_weapon_review`. Approval grants the weapon; rejection frees the slot. Every decision is written to the audit table.

---

## Commands

| Command | Purpose | Access |
|---|---|---|
| `/gswd` | Open the design studio | Everyone (or configured jobs) |
| `/gswd_armory` | Open the personal design library | Everyone (or configured jobs) |
| `/gswd_weapon_review` | Open the staff review queue | ACE `gswd.weapondesigner.admin` |
| `/gswd_claim_designer` | Claim a purchased designer pass | Everyone |
| `gswd_tebex_grant` | Grant a designer pass | Server console, or ACE `gswd.tebex` |
| `gswd_pool_status` | Print per-weapon slot occupancy | Server console only |
| `gswd_rescan` | Re-scan the weapon templates folder | Server console only |
| `gswd_rebuild_pool` | Rebuild every weapon slot pack | Server console only |
