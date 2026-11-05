# Troubleshooting

## Missing machines or incorrect placement

Start the separately installed matching map, enable the same shops in its config and config/locations.lua, and keep all 63 streamed files. Check shop origins/headings and the shared shop-space layouts. The package contains machine/product props, not the SWIRLZ building MLO.

## No interactions or permission denied

Check the selected framework/inventory and target in the startup console. Start dependencies before this resource. Native prompts use E and G. Staff need the job of that location; another shop's job does not grant access. Bosses additionally need an eligible grade and proximity to the correct desk.

## Items, metadata or storage missing

Merge all 19 item definitions and icons, then restart the inventory. Check the selected adapter and the installed inventory version. Core ESX inventory lacks shared stash/metadata features; use a supported external inventory or disable unsupported features. The item fragments must be pasted inside an item table.

## Craft fails or reports too fast/full

Carry all recipe ingredients, use the correct machine and finish within its configured timing. The server validates job, distance, reservation token, minimum elapsed time and inventory results. Wait for the machine reservation to clear if another player owns it; moving away/disconnect cleanup releases stale reservations.

## Orders cannot be placed at startup

Saved orders must finish restoring and an enabled society account must initialize before charging. Check oxmysql connectivity/table permissions, startup errors and configuration before retrying. Do not repeatedly pay through a broken provider. An invalid basket, remote position, insufficient funds or per-player limit also refuses the order.

## Tray or supply access denied

Customers collect only their own ready order at the matching tray. Staff fill only their own shop's trays from inventory. Supply orders belong to the ordering character, collected at the supplier or that order's shop door. Full inventories leave remaining items waiting. Transfers during order settlement are temporarily refused.

## Refund or balance issue

Check the original payment account/character, society transactions, open orders and pending rollback credits in resource KVP. Failed packing/deposit rollback credits retry when the original character is online and the framework accepts the credit. Keep that storage when migrating servers. Normal updates do not erase it; interrupted cross-resource transactions may need manual reconciliation.

## UI blank, search or translations wrong

Keep the entire compiled web directory, including fonts.css and fonts/. The font is local; no external font request is required. Native DUI projection and cursor hit testing still depend on the configured screen geometry. Check locale keys/placeholders and merge inventory item labels separately.

For support include version 1.0.0, framework/inventory/target versions, enabled location/job, reproduction, console errors and whether a restart/crash occurred. See [Support & Discord](../../support.md).
