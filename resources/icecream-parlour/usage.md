# Usage

## Staff preparation

Use the shop job appropriate to the location. Buy ingredients on the staff monitor's SUPPLIES tab, then collect from the Cypress Flats supplier or choose delivery to that shop's door. Carry the ingredients while crafting.

| Machine | Workflow |
|---|---|
| Waffle iron | Pour batter, close/bake, press OPEN in the golden band; one mix yields three cones |
| Soft serve | Select vanilla/chocolate/twist, hold the lever and release in the green band |
| Gelato | Choose one of six flavours, hold to scoop onto a cone |
| Mixer | Choose one of four shakes, hold to blend and release for quality |

Each machine has its own world screen and camera. Ingredients are consumed at successful finish. Only one player reserves a machine at a time. Nearby players see moving parts and product assembly.

## Customer orders

Use a counter tablet, select products and pay cash or card. Keep the order number and pickup tray number. Staff see the shop's queue on the monitor and fill the tray from their inventory. The wall board shows NEW and READY orders.

With ox_inventory, authorized staff/customer access is enforced through stash hooks. Other supported inventory paths use direct item handover. The ordering character collects only their own ready order, at the correct tray, within range. Full inventories leave uncollected contents waiting.

Staff deletion can return tray items and refund an uncollected order. If the customer is offline or the refund fails, deletion is refused instead of discarding the order. Read [Economy & Storage](economy.md) for expiry and external deletion behavior.

## Boss and storage

Boss grades manage only their own shop job from its desk: hire by an offer the target answers, promote/demote lower ranks, fire eligible staff, deposit and withdraw society money. A boss cannot change their own rank through this panel or promote someone to their own rank.

Use the back-counter stash for shared staff storage where your inventory supports it. Finished items are usable for eating/drinking and configured needs effects. Core ESX inventory has no shared stash; disable it or use a supported external inventory.
