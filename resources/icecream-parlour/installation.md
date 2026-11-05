# Installation

## Dependencies and startup

Install the matching SWIRLZ map separately. The default integration expects `10store-swirlz`; keep its enabled locations aligned with config/locations.lua. Custom interiors require measured placement/camera/target configuration.

Start the stack in this order, choosing the providers you use:

```cfg
set onesync on
ensure oxmysql
ensure qbx_core              # or qb-core / es_extended
ensure ox_inventory          # or your configured supported inventory
ensure ox_target             # optional; qb-target or native prompts work too
ensure 10store-swirlz
ensure gs-icecream-parlour
```

Keep the complete client/, server/, shared/, bridge/, config/, locales/, stream/, web/ and notices. Customers do not need npm or a UI build. The script spawns its props; the supplied map's built-in prop copies step aside while this resource is active, as described in the supplied map integration guide.

## Items and icons

Merge, do not replace, your inventory definitions:

| Provider | Installation input |
|---|---|
| ox_inventory | install/items_ox_inventory.lua into its item table |
| QBCore-style inventory | install/items_qb_core.lua into its item list |
| ESX default inventory | install/esx.sql |

Copy all **19** icons from install/images/ to the inventory image directory. The included PowerShell/bash helpers accept an explicit destination and skip existing icons unless overwrite is requested. Restart the inventory after merging.

The items comprise five purchased ingredients/packaging entries, waffle cones and thirteen finished usable products. Manually imported Lua item fragments belong inside an existing table; they are not standalone resource scripts.

## Jobs and accounts

Job.Register creates missing shop jobs when supported; existing jobs are reused. The fallback definitions are in install/jobs.lua or install/esx.sql. Default grade 3 is Owner, with lower grades Trainee, Scooper and Shift Lead.

Assign your first owner using your framework's admin tools, for example `/setjob <id> icecream_hawick 3`. This is a framework command, not a command added by this resource.

The society module creates its two tables automatically. install/society.sql contains the schema if your database is managed manually. Initial balances are zero and legacy balance import is disabled.

Test the full workflow with two players and repeat at all enabled shops. See [Economy & Storage](economy.md) and [Troubleshooting](troubleshooting.md).
