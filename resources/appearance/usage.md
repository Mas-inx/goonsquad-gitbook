# Usage

This page covers the player flows: entering a shop, using the editor, saving outfits, and first-character creation.

---

## Entering a Shop

Walk into a store zone. A prompt appears while inside it:

```text
[E] Clothing Store
```

Press **E** to enter. The prompt text comes from `Config.ShopInteractLabels` for that shop type, and the key from `Config.StoreInteractControl`.

The server re-checks the player's distance to the store when the open request arrives, allowing the store radius plus a three-meter buffer, so a spoofed request from across the map is rejected. A store with a job or gang lock only opens for a player who holds a listed job or gang at the required grade.

Four shop types exist, each opening a different editor:

| Shop | Opens |
|---|---|
| **Clothing** | A hub — Buy Clothes or Saved Outfit |
| **Barber** | Hair, beard, eyebrows, makeup, blush, lipstick, skin, and body |
| **Tattoo** | Tattoos by body zone, with a live preview |
| **Surgeon** | Inheritance and head blend — face, skin, race, parents, and mix |

What each shop offers is set by `Config.StoreMenus`. See [Configuration](configuration.md#store-menus).

---

## The Clothing Store Hub

A clothing store opens on a two-choice hub:

| Choice | Result |
|---|---|
| **Buy Clothes** | Opens the section menu, or the full editor when only one section is configured |
| **Saved Outfit** | Opens the outfit browser to wear something already saved |

Back from either returns to the hub, so a player can change clothes and then load an outfit without leaving the store.

---

## The Editor

The ped stays in the middle of the screen. Categories sit on arc-shaped rails to the left and right, the item carousel runs along the bottom, and the action dock sits bottom-left.

### Categories

| Rail | Contents |
|---|---|
| Right | Tops, undershirts, arms, bottoms, shoes, hats, glasses, ears |
| Left | Masks, chains, vests, bags, decals, watches, bracelets |

Each rail holds five circles at a time; the `outerwear` and `extras` overflow rails reach the rest.

### Selecting items

- **← / →** cycle through the items in the selected category
- The carousel shows a real render of each item, not a number
- The variant bar under the carousel steps through an item's textures and colors
- Items restricted by an [item rule](administration.md#item-rules) do not appear

### Feature editors

Barber, makeup, tattoo, ped, and face-feature sections use a dial instead of a carousel. The dial sets style, opacity, or a slider value; the palette beside it sets color, from 64 hair swatches or the matching overlay palette.

Face features are 20 sliders from −1.0 to 1.0 in steps of 0.1. Head blend is the parent pair plus a mix dial, with 46 parent faces per side.

{% hint style="info" %}
The editor seeds itself from the live ped once and then hands authority to the UI, so saving from a shop that does not expose the face sliders never flattens a sculpted face back to defaults.
{% endhint %}

### Camera

| Input | Action |
|---|---|
| Left mouse drag | Rotate |
| Right mouse drag, or wheel | Zoom |
| Middle mouse drag | Pan |
| **A** / **D** | Rotate |
| **Z** / **X** | Zoom |
| **Q** / **E** | Tilt |
| **R** | Reset |

The focus bar frames a body zone in one click — face, shirt, pants, or shoes. Reset animates back to a full-body framing.

While the editor is open, conflicting game controls and player firing are disabled.

### Keyboard shortcuts

| Key | Action |
|---|---|
| **Esc** | Back, or exit |
| **Enter** | Save, or Select / Wear in a browser |
| **O** | Save outfit |
| **← / →** | Cycle items |
| **A / D** | Rotate camera |
| **Z / X** | Zoom |
| **Q / E** | Tilt |
| **R** | Reset camera |

---

## Saving

**Enter** saves the appearance to `playerskins` and closes the section. On ESX the same appearance is mirrored best-effort into `users`.`skin`, so legacy scripts that read it keep working.

The shop cost is charged on save, once per visit — moving through several sections of the same shop menu is billed once, not once per section. Tattoo shops with `Config.ChargePerTattoo` enabled bill each tattoo instead.

The character creator never charges.

---

## Outfits

### Saving an outfit

Press **O** in the clothing editor, type a name, and confirm. Outfits are unlimited.

Saving a name that already exists updates that outfit in place rather than creating a duplicate, and every save is verified by reading the row back before it reports success.

### Wearing an outfit

Open the outfit browser from the clothing-store hub with **Saved Outfit**, or with the `/outfits` command. The browser is a vertical rail: cycle to an outfit, preview it on the ped, and press **Enter** to wear it.

The list contains the player's own outfits plus any job or gang uniforms they qualify for, prefixed `job_` or `gang_`.

### Outfit codes

An outfit's owner can mint a shareable code from the outfit browser. Anyone who enters that code gets a copy of the outfit saved under a name of their own choosing.

- Codes are `Config.OutfitCodeLength` characters, default 10
- The alphabet excludes the ambiguous `0`, `O`, `1`, and `I`
- A code is minted once per outfit and re-issued on request, so the same outfit always shares the same code
- Only the owner can mint a code for an outfit; importing one clones it, it does not transfer it

### Job and gang uniforms

Uniforms live in `management_outfits` and are filtered by the player's job or gang, their grade, and the model's gender before they reach the list. A uniform is cached per character while it is worn; `Config.PersistUniforms` decides whether that survives a disconnect.

Other resources can push a uniform onto a player directly — see [API & Exports](exports.md#outfit-and-uniform-events).

---

## Character Creation

A brand-new character is taken into the creator automatically on first spawn. Which event triggers it depends on the framework:

| Framework | Trigger |
|---|---|
| Qbox | `qb-clothes:client:CreateFirstCharacter` from `qbx_core`, plus the `qbx_properties` apartment hand-off |
| QBCore | `qb-clothes:client:CreateFirstCharacter` from `qb-multicharacter` |
| ESX + `esx_multicharacter` | `esx_skin:openSaveableMenu` |
| ESX + `esx_identity` | `esx_skin:resetFirstSpawn` → `esx_skin:playerRegistered` |
| ESX, neither installed | `esx:playerLoaded` with `isNew` |

On ESX all three paths funnel into a single creator behind a 60-second claim lock, so two surfaces never fight over NUI focus.

The creator dock steps through ten tabs: ped, inheritance, face features, appearance, makeup, clothes, props, tattoos, outerwear, and extras. Finishing saves the appearance and hands the player back to the spawn flow.

A returning player is never sent back through the creator. A row left behind by `qb-clothing` or `esx_skin` counts as "has an appearance" and is converted instead — see [Integrations](integrations.md#migrating-from-a-predecessor).

`/gscreatechar` reopens the creator manually. It is admin-only and validated on the server.

---

## Commands

| Command | Purpose | Access |
|---|---|---|
| `/gsadmin` | Open the admin store map | Admin |
| `/outfits` | Open the outfit browser | Player |
| `/reloadskin` | Reapply the saved appearance | Player |
| `/gsbarber` | Open the barber editor | Player |
| `/gstattoo` | Open the tattoo editor | Player |
| `/gsrelease` | Emergency release — restores NUI focus, ped control, camera, and screen fade | Player |
| `/gscapcheck` | Print how preview images resolved on this client | Player |
| `/gscreatechar` | Force-open the character creator | Admin, server-validated |
| `/gsdiag` | Install diagnostics for the caller | Player, 10 s cooldown |
| `gsdiag <playerId>` | Install diagnostics for a player | Server console |

The command that opens the admin map is `Config.AdminCommand`; the rest are fixed.

{% hint style="info" %}
`/gsbarber` and `/gstattoo` open their editor anywhere, without a store zone. Shop costs still apply on save. If you want barbers and tattoos to be an in-world activity only, tell players to use the shops — these are convenience commands, not zone-gated ones.
{% endhint %}

`/reloadskin` has a `Config.ReloadSkinCooldown` cooldown, five seconds by default.

`/gsrelease` is the escape hatch: if a script conflict ever leaves a player stuck with NUI focus or a frozen ped, it restores everything without a reconnect. It is also available as the `ForceRelease` export.
