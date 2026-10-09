# Usage

## Commands

| Default command | Action |
|---|---|
| `/glowup [stats\|goals\|awards\|top\|bag]` | Open the career app; defaults to stats |
| `/sellglow` | Select a carried cosmetic and call a buyer |
| `/glowpopup` | Toggle consecutive automatic buyers |
| `/glowstats` | Open personal statistics |
| `/glowgoals` | Open daily/weekly goals |
| `/glowawards` | Open achievements |
| `/glowtop` | Open the earnings leaderboard |
| `/glowlang <code>` | Save a language preference: `en`, `es`, `de`, `fr`, `pt-BR` |
| `/glowuse <item>` | Use an item in the built-in bag; external inventories ignore this command |

Names can be changed or disabled in `Config.Commands`. The bag tab is available with the built-in inventory.

## Controls

| Context | Controls |
|---|---|
| Supplier, mixer, packing or thief search | Configured target interaction, or E for built-in prompts |
| Equipment placement | WASD move, Q/E rotate, Shift move faster |
| Table placement height | Mouse wheel or Up/Down arrows |
| Place equipment | Left-click or Enter |
| Cancel placement | Right-click or Backspace |
| Mixer START close-up | Click the glowing button or press E |
| Cancel START prompt | Right-click, Backspace/Esc or X |
| Cancel running craft, application or thief search | X |
| Close an interface window | Esc |
| Switch career tabs | Q/E while the app is open |

The fixed placement and crafting controls are not resource-defined key mappings. Target/helper resources may provide their own rebindable controls.

---

## Buy and Deploy Your Studio

Visit **Studio Supplier** at the Vespucci location marked on the map. Buy the **Makeup Workbench**, **Pigment Mixer** and ingredients for a recipe. Materials support quantities up to 50 per cart line; equipment is limited to one of each per checkout. Pay with cash or bank when your framework provides both, or your standalone wallet.

Use the workbench inventory item. Position its preview, rotate and adjust height, then confirm. The case unfolds into a table. Use the mixer near your own table: the glowing footprint shows its reserved spot. Move and rotate the preview until it snaps into position, then confirm.

The default limit is one table and one mixer per owner. To relocate deployed equipment, use a spare equipment item: it moves the existing unit and keeps the spare. Alternatively, use **Pack up** to recover the deployed item, then place it again. Moving a table moves its attached mixer; packing a table returns both. Equipment in an active craft cannot be packed or moved.

## Craft Cosmetics

Interact with your mixer to open the crafting interface. Choose one of eleven recipes, review the ingredient counts, and select a batch of one to five. The craft button becomes available when you hold the required ingredients.

Your character walks into position and the camera focuses on the machine's START button. Click the button or press E to begin. The camera widens and the machine dispenses one cycle per cosmetic. Higher ranks shorten crafting time.

Ingredients are reserved when the craft begins. Cancelling returns them; if an inventory refund cannot fit, the resource records owed items and retries delivery every 20 seconds. Successful crafting grants products and XP. Use **Sell 1** for a carried cosmetic, or **Use 1** if application is enabled.

## Cosmetic Catalogue

| Product | Item | Base sale value |
|---|---|---|
| Velvet Lipstick | `gs_lipstick` | $650 |
| Glass Lip Gloss | `gs_lipgloss` | $520 |
| Lash Sculpt Mascara | `gs_mascara` | $580 |
| Precision Eyeliner | `gs_eyeliner` | $480 |
| Dusk Eyeshadow Palette | `gs_eyeshadow` | $980 |
| Silk Foundation | `gs_foundation` | $820 |
| Rose Blush | `gs_blush` | $560 |
| Pearl Highlighter | `gs_highlighter` | $700 |
| Pro Brush Set | `gs_brush_set` | $1,150 |
| Lock Setting Spray | `gs_setting_spray` | $610 |
| Sculpt Contour Kit | `gs_contour` | $890 |

Actual NPC payments apply random variation, rank bonuses and possible tips. Server owners can change these values.

---

## Sell to Buyers

Use `/sellglow`. If you carry several cosmetics, choose a product in the picker. A buyer walks toward you and either pays, refuses or steals. Products are reserved before the approach; a refusal returns them.

`/glowpopup` keeps buyers arriving while products remain. Each buyer chooses one carried cosmetic and requests one to three units. The stand pauses during menus, crafting, vehicles and thief chases, and stops when stock is exhausted. Toggle the command again to stop future buyers; an active sale still finishes.

If a buyer steals, stop the thief and interact with **Search thief** when they are downed. Complete the search before the stolen record expires, by default five minutes from sale creation. Cancelling the search does not recover the products. A successful search returns the stolen quantity and grants recovery XP.

The normal client flow allows one active thief at a time. Unresolved sales are settled by the server timeout; stolen records are temporary and do not survive a resource/server restart.

## Apply Cosmetics

Application is optional and off by default. When enabled, use a cosmetic through your inventory, the standalone bag or **Use 1** in the crafting menu. An application animation plays, and X cancels without consuming it. Successful application consumes one item unless configured as reusable.

Lip, eye and cheek products apply native makeup overlays to freemode peds. Foundation fades existing skin overlays. The look lasts until your appearance system loads the saved appearance again. The brush set and setting spray have animations without persistent buffs.

## Grow Your Career

Open `/glowup` to see XP, rank, perks, earnings, tips and studio activity. Crafting, successful sales and recovery grant XP. The ten ranks increase payouts, crafting speed and tip chance.

Goals rotate daily and weekly on UTC boundaries. Complete them and click **Claim** before their reset. Achievements unlock automatically as lifetime milestones are reached; claim each reward from **Awards**. The leaderboard ranks lifetime NPC sale earnings and includes your own standing.

In built-in inventory mode, the **Bag** tab lists carried items and wallet balance. It supplies usable-item buttons and equipment placement shortcuts for the shipped item names.
