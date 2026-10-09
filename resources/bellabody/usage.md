# Usage

## First Clinic Session

1. Visit the Clinic Supplies vendor near Mount Zonah.
2. Buy an Operation Table, Portable Crafting Kit, syringe and fat extract. A Small BBL Shot needs one of each ingredient; a Big BBL Shot needs two fat extracts.
3. Use the kit from your inventory or the standalone bag. Select a recipe and batch size, then craft. X cancels the built-in progress routine; ingredients are refunded on cancellation.
4. Use the table item and place it on a flat surface with clear space around both long sides.
5. Stand within 40 m of your owned table and use `/bblclient`.
6. Wait for the phone call and customer arrival. Press E to accept the offer or G to decline within 30 seconds.
7. Follow the client to the table and treat her within 60 seconds. Use the target/E interaction on the patient or table, or use a shot item nearby.
8. Choose the matching shot. Watch the synchronized procedure and cinematic camera; X stops the procedure early.
9. Complete treatment to receive the server-calculated payment and XP. The client gets off the table and leaves.

A Big BBL Shot can upgrade a Small BBL request for a 1.35× offer and bonus XP. A Small BBL Shot cannot fulfill a Big BBL request. No shot is consumed when the wrong lower-tier shot is selected.

---

## Placement Controls

| Control | Action |
|---|---|
| W / A / S / D | Move the preview relative to the camera |
| Q / E | Rotate |
| Mouse wheel / Up / Down | Adjust height |
| Shift | Faster movement and adjustment |
| Left click / Enter | Confirm a valid placement |
| Right click / Backspace | Cancel |

The preview snaps to ground height and shows placement validity. Cancelling keeps the item. Packing an idle table returns it to the inventory. A table cannot be packed during an active customer session. To move an existing table at the placement limit, use another table item; it stays in the inventory after the move.

## Procedure Cancellation and Refunds

Starting treatment reserves one shot. Cancelling returns it and gives the patient 20 more seconds of patience by default. Craft cancellation returns reserved ingredients. Resource-stop and unload hooks also refund active crafting/procedure items.

If an inventory cannot hold a refund, it is recorded on the profile as owed items. The server checks for room every 20 seconds. Leave inventory space available and wait for the return notification.

The default custom procedure has no progress bar. Its camera and scene run for the configured server duration, plus walking and animation transitions. With the custom surgeon scene disabled, the fallback procedure uses the selected progress adapter.

## Commands

| Command | Purpose |
|---|---|
| `/bblclient` | Summon a walk-in to your table |
| `/clinic [stats\|goals\|awards\|top\|bag]` | Open a career-hub page |
| `/clinicstats` | Personal career statistics |
| `/clinicgoals` | Daily and weekly objectives |
| `/clinicawards` | Permanent achievements |
| `/clinictop` | Earnings leaderboard |
| `/cliniclang <code>` | Set `en`, `es`, `de`, `fr` or `pt-BR` |
| `/clinicuse <item>` | Use an item when the internal inventory is active |

These are the eight intended configurable player commands. There are no development, item-grant or admin-money commands.

Examples for the internal inventory:

```text
/clinic bag
/clinicuse crafting_kit
/clinicuse operation_table
/cliniclang es
```

Using a shot without a waiting nearby patient shows a hint and does not consume it.

## Career Hub

- **Stats:** rank, XP, earnings, procedures, crafting and current perks
- **Goals:** daily/weekly progress, reset countdowns and claimable rewards
- **Awards:** six permanent milestones and one-time rewards
- **Leaderboard:** highest lifetime procedure earnings and your standing
- **Bag:** internal inventory contents, usable items and table placement

Goals and awards need a manual claim after completion. Repeated claims are rejected. The bag tab is hidden when an external inventory is active. Use Q/E to cycle the hub tabs and Escape to close the interface.

## Customer Behavior

One walk-in can be active per player. Customers choose one of five appearances and a weighted procedure request. If navigation times out, the client is repositioned closer to the player. Offers expire; a patient who waits too long on the table leaves. Expired offers and patient timeouts add to the lost-customer statistic.

NPC clothing is preserved by default. A completed procedure does not alter the player's appearance or body model.
