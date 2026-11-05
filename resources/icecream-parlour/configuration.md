# Configuration

Twenty-four explicit Lua files stay editable under Asset Escrow: eight config files, two locales, four framework bridges, five inventory bridges, target and notify bridges, and three install snippets. Core recipe/layout/client/server implementation has no broad ignore entries. SQL, icons and instructions remain install assets.

| File | Settings |
|---|---|
| config/config.lua | Provider selection, language, branding, prompts, session guards, quality, metadata, sounds, effects, consumption |
| config/locations.lua | Enabled shops, world origin/heading and job names/labels |
| config/job.lua | Job creation, grades/pay, boss desks and society behavior |
| config/products.lua | Flavours, recipes, ingredients, output items and consumption effects |
| config/machines.lua | Machine placement, models, camera/screen/target geometry, timing and quality bands |
| config/orders.lua | Prices, payment methods, service fee, limits, timers, tablets/trays/monitor/TV |
| config/supplies.lua | Wholesale catalogue, fees, pickup NPC and per-shop courier delivery |
| config/storage.lua | Shared staff stash placement, capacity and job access |

## Providers and defaults

Framework, inventory, target and notification selection default to auto. Framework preference is Qbox, QBCore, then ESX. Inventory adapters are tried in manifest order; force the desired provider if several run. Native interaction uses E, with G for the alternate option at shared spots. No supported inventory means item operations refuse to proceed.

Debug is false. Locale is en, with de included. Every screen uses the bundled local Outfit font. Procedural sounds are enabled at volume 0.65; optional file overrides must also be declared in the manifest.

Session defaults are a 4 m general server interaction range and a 350 ms per-request interval. Machines, orders, supplies and boss desks also apply their own distances and job checks.

## Shops and placement

All three locations start enabled. Their jobs are icecream_hawick, icecream_mirrorpark and icecream_delperro. A shared job name shares staff and society funds across those locations. Different job names isolate businesses. Keep job names stable across updates.

Layout coordinates are in shop space: x to the right looking inward from the front door, y toward the back wall and z above the origin. Origin/heading transforms place the same layout in every enabled shop. Move the origin only with the matching map placement; change layout coordinates when adapting furniture.

Each enabled shop gets four machines, two tablets/trays, a monitor, order TV, boss laptop, stash and delivery door. Disabling a location should match the separately installed map's location configuration.

## Jobs, quality and consumption

Job.Enabled false opens job-gated machines/monitors/storage to everyone. Job.Register true creates missing jobs. Boss access additionally requires the configured job, a boss grade and proximity to that job's own desk. Default grades pay 50/70/95/140, with grade 3 as Owner.

Perfect hold quality uses the 0.90–1.04 band around a 3200 ms fill; the server rejects finishes below its minimum time. Waffles use a 7000 ms bake and a 0.84–1.02 quality band. Do not shorten animations/timers independently of server constraints.

Quality, maker and tooltip metadata depend on inventory metadata support. Consumption uses male/female grip/animation settings and configurable hunger/thirst/stress effects. Brain freeze defaults to a 60% chance when another item is consumed within 45 seconds. Config.Consumables.onConsume is an optional server hook.

## Locales

Copy locales/en.lua, change its Locales key and set Config.Locale to the new code. Missing keys fall back to English. Preserve format placeholders, GTA input tokens and intentional HTML tags. Inventory item labels are translated in your own inventory definitions too.

See [Economy & Storage](economy.md) for persistence and [Integrations](integrations.md) for adapter behavior.
