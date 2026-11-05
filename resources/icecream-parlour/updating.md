# Replacing a development installation

This is the **1.0.0 new release**. Keep the resource name `gs-icecream-parlour` because exports, DUI URLs and KVP state belong to it.

1. Stop the resource and back up its folder, your configuration/locale/bridge modifications, inventory item definitions and job changes.
2. Back up the server's resource KVP storage and the `gs_icecream_society` / `gs_icecream_society_transactions` tables. Orders, supply pickups, counters and pending rollback credits use KVP, not files inside this folder.
3. Replace code, streamed props and the complete web directory together. The release already contains minified UI and the local font.
4. Merge your locations/jobs, recipes, machines, prices, supplier/delivery settings, locale and integrations. Keep `importLegacyBalance = false` for a new installation unless you deliberately plan a legacy migration.
5. Merge the 19 item definitions/icons into the inventory. Start the database, framework, inventory, optional target and matching map before the script.
6. Assign the first boss of each enabled shop through your framework. Existing job definitions are reused; verify their grades and boss flags.
7. Test with two players at every enabled shop: craft, order/pay, fill and collect, refund, pickup/delivery, boss money/staff operations and restart persistence.

Twenty-four explicit Lua paths remain editable under escrow. Preserve changes in config/, locales/, bridge/ and the three install Lua snippets; core implementation is protected after portal processing.

KVP state is not shipped in the ZIP. Moving to a different resource name or deleting server KVP storage loses the association with existing orders and pending rollback credits. Renaming a shop job creates a different society account; the old account remains under its original name.


See [Installation](installation.md), [Configuration](configuration.md) and [Economy & Storage](economy.md).
