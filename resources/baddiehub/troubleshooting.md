# Troubleshooting

| Symptom | Check |
|---|---|
| Character still loading / hub unavailable | Framework start order, SQL connection, schema/migration logs and a fully loaded character |
| Database initialization failed | Database create/alter permissions, all ten tables and public-ID column; repair and restart |
| Nobody can enter | Gate mode, framework gender, female model configuration, staff override or Discord role setup |
| Custom female ped denied | Add its model, inspect framework gender; remembered models never override a male framework verdict |
| Discord roles seem stale | Ten-minute definitive cache; inspect bot/guild/roles/player Discord identifier and API errors |
| Empty image placeholders | Item definitions, copied PNG names, selected inventory and `Config.ItemImagePath` containing `%s` |
| Rewards fail | Verify money/item adapters, item names, capacity and actual operation return values |
| Standalone rewards fail | Implement real money/inventory hooks or disable those rewards; no internal wallet or bag exists |
| Starter car unavailable | `enabled`, `show`, detected garage requirement and configured resource names |
| Car owned but missing / wrong garage | OneSync, vehicle model/type, ownership schema, garageName, stored-state mapping and server logs |
| No usable keys / fuel mismatch | First detected key/fuel adapter and its current export/event contract |
| Friend request cannot be resent | Decline cooldown, block list, request window, friend limit and target membership |
| DM denied | Default friends-only rule, sender/recipient membership and blocks in either direction |
| Chat XP stops increasing | Minimum eight characters, cooldown and default 50 XP per ten-minute cap |
| Season not reset by renaming | Name is display-only; existing XP and claimed levels stay in SQL |
| Staff commands denied | Admin ACE or command-specific ACE; framework moderator membership alone is insufficient |

## Failed and Partial Delivery

When no starter-pack components are delivered, the claim is released for retry. Partial bundles stay claimed to prevent duplicate payouts; check the missing-component console entries and claims webhook and repair only the missing components. The Welcome Box is restored on complete delivery failure where the inventory accepts it. Do not reset a partially successful claim without accounting for already delivered money/items. A vehicle claim can remain recorded after an error following ownership creation; verify ownership before authorizing a reissue.

## Updates and Support

Back up the hub database, configuration, directory files and private secrets. Preserve the resource name and runtime assets. Keep old claim/progress records when updating. Do not distribute the local preparation ZIP directly: upload to Cfx Portal, let it encrypt protected Lua, then distribute the processed asset through Tebex. NUI runtime files remain readable/minified because [Asset Escrow does not cover NUI](https://docs.fivem.net/docs/server-manual/asset-escrow/).

For support, provide version, framework, inventory, garage/key integrations, relevant console errors and reproduction steps. Redact tokens, webhook URLs and player identifiers. Temporarily enable `Config.Debug` for diagnosis and disable it afterwards. Live tests on your actual FiveM setup remain necessary for garage, inventory and multiplayer behavior.
