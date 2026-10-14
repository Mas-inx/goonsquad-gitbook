# Troubleshooting

Temporarily enable `Config.Debug`, reproduce once and capture complete server/F8 logs. Return it to false after diagnosis. The release includes no development or position-printing commands.

## Resource Will Not Start

Keep the folder name `gs_laflavia`. Start oxmysql, the framework and inventory first. OneSync state awareness must be enabled. Confirm all files from the processed package are installed; mixed versions or duplicate copies can leave scripts and props out of sync.

## No Station or Staff Interaction

- Install the La Flavia map for the default coordinates, or relocate the complete bar configuration.
- Assign the `laflavia` job and confirm the client sees its update.
- Check `Config.Target`, the chosen target's state, zone distance and backend selection.
- Native prompts use E/G; customer access does not grant staff interactions.
- Standalone selection has no inventory or money support and is not a full deployment.

## Job or Boss Menu Missing

Check the startup job-registration message. An existing framework job is reused; it needs an appropriate boss flag. Merge `install/jobs.lua` or use `install/esx.sql` when auto-registration is unavailable. The boss must be within 3 meters of the laptop and cannot manage themselves or a rank at/above their own.

If a Qbox staff list is empty, confirm the current adapter is installed: it supports the group-members map as well as a legacy list. Grade-three lift access is separate from the framework boss flag.

## Missing Items, Images or Bottle Volume

Merge all definitions and restart the inventory. Item names must match models and rewards exactly. Copy `install/images/*.png` to the correct image directory. Bottle info should retain `remaining` milliliters and bottles should be non-stackable where supported.

Default ESX inventory lacks writable bottle metadata, so whole-bottle consumption is expected. A custom adapter that reports metadata support but cannot write it must be corrected or have that capability disabled. A refused metadata update or reward addition rolls back the applied ingredient changes.

## Storage Does Not Open

Verify the staff job, shelf range and chosen inventory API. Default ESX has no stash support: disable `Storage.Enabled` or install/implement a supported backend. Preserve existing stash IDs when moving the location.

## Tablet or Ice Screen Is Blank / Clicks Miss

Check the resource name, `web/dui/` files, image paths and streamed props/YTYPs. Use the supplied complete release. If you changed a model, update its texture names, screen quad/arc and shared layout rectangles together. DUI distance is intentionally shorter than prop spawn distance.

## Recipe Cannot Start or Finish

Read startup recipe errors. Every referenced object must exist, and the reward/ingredients must be registered. One player occupies each station/machine. The server checks job, token, range, required items and minimum duration; sending a completion early cannot award a drink.

Iced recipes require carrying the glass to the machine, dispensing and serving there. The carry timeout is 180 seconds and the distance limit is 60 meters. A malformed step report is handled as missing/poor measurement, not a reward grant. Check inventory capacity when the finished item cannot be awarded.

## Order Remains Waiting / Cannot Be Deleted

Fill every requested drink at the correct tray. Only requested drink names are accepted by the stash hooks. Staff must be behind the counter and close to the monitor/tray; customers must stand at their own ready tray.

When a refund is due, an offline customer must join before cancellation can settle. The order remains intact rather than dropping the paid amount. A refusing/full staff inventory or failed refund also preserves the order. Ready-order expiry and partially collected orders are not refundable by default.

## Supplies Cannot Be Collected

The order belongs to its purchasing character. Stand at its chosen pickup point or delivery gate; knowing a stash ID does not allow remote access. The newest matching order is collected first. If the inventory is full, remaining supplies stay waiting. Default supplier expiry is off; a positive configured expiry removes uncollected stock without a refund.

## Finances Unavailable or Balance Unexpected

Start oxmysql first and confirm table creation. `Job.Enabled` and `Job.Society.enabled` must be enabled. A new account starts at zero; import is off by default. Keep account IDs stable. Enabled refunds debit the society's original income share, which can make it negative after bosses have spent that money.

A transaction-log warning means the money update committed but its audit entry could not be saved; repair the database issue before relying on the history. Failed player withdrawal payments restore the society amount. Supply purchases use staff cash/bank, not automatic society billing.

## After Restart / Server Migration

Keep the resource name, KVP state, SQL tables and inventory stashes together. Open orders and supplies are persistent, but active preparation scenes and client intoxication are not. Do not copy only the script folder and expect the former server's KVP orders to transfer.

## Support Details

Include the resource version, framework/inventory/target names and versions, complete error logs, affected recipe/order/machine ID and the configuration files you changed. Do not send private credentials from external integrations.
