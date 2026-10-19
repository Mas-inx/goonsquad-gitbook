# Updating

1. Back up `data/scales/`, your configuration, bridge changes and the SQL table if enabled.
2. Stop the resource. Replace runtime files while keeping the resource name `gs-scalemenu`.
3. Preserve existing character JSON records. Fresh packages intentionally contain no player data; keep the data directory and write permission.
4. Merge configuration/bridge changes rather than replacing your customized files blindly. Keep identifier rules stable to retain ownership of existing records.
5. With database mode, retain the SQL table and JSON recovery copies. The included schema creates a table; it is not a data reset script.
6. Restart and test reconnect persistence, profiles, character switches, live/manual application, vehicles, cameras, combat integration and admin access.

This initial free release is **1.0.0**. Only `config.lua` and `shared/framework.lua` are explicit Lua escrow exceptions. Use the maintained source to rebuild browser changes; existing locale JSON can be edited directly.
