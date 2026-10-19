# Updating an Existing Installation

This refreshed build keeps version **1.0.0** and adds QBCore and ESX Legacy support alongside Qbox.

1. Back up the resource, `gs_nails_designs`, framework character metadata and any server resource KVP used by older ESX builds.
2. Stop GS Nails and replace the runtime files with the latest portal-delivered package. Keep the resource name `gs_nails`.
3. Merge your settings into the new `config.lua`. It adds `Config.Framework` and the `skinchanger:modelLoaded` appearance event. Preserve locations, job definitions, metadata keys, media settings and enabled gender packs.
4. Restore private credentials in `server/credentials.lua`. Review custom bridge changes against all three current bridges; include the new `shared/bridge.lua`.
5. Start the core and required dependencies first. Follow [Installation](installation.md) for ESX job/society setup if applicable.
6. Test staff/customer sessions, accepted and declined payments, saved designs, reconnect and character switch, appearance reloads, duty, management and gestures.

Keep the shipped catalog, templates, both metadata files, finished streamed packs and production UI together. Customers do not rebuild assets. Changing frameworks requires a deliberate migration of character identifiers and metadata; keeping the same framework and metadata key preserves existing selections.
