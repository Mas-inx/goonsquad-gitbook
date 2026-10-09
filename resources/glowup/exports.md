# API & Exports

GS GlowUp 1.0.0 has no public gameplay exports for granting XP, items or money, forcing sale outcomes, or querying profiles. Its supplied integration surface is the open bridge/persistence configuration and inventory use callbacks.

## Inventory Lifecycle Exports

In standalone framework mode, usable-item registration creates server exports named after registered items. These callbacks accept an inventory lifecycle event, item data and inventory data. They dispatch use when the event is `usingItem` and the inventory has an ID.

Wire them through the inventory item definition rather than calling them as item-grant APIs:

```lua
server = { export = 'gs-glowup.makeup_workbench' }
```

Equipment uses the configured item name. Cosmetics register only with `Config.ProductUse.enabled = true`. Supported framework installations register use through their framework instead. See [Installation](installation.md#usable-items).

---

## Internal Request Protocol

The client requests server actions through `gs-glowup:server:request(reqId, name, data)`. Responses return on `gs-glowup:client:response(reqId, result, error, extra)`. This is internal gameplay transport, not a documented unrestricted public API.

| Request family | Internal actions |
|---|---|
| Initialization and career | `player:init`, `hub:data`, `hub:leaderboard` |
| Shop | `shop:catalog`, `shop:checkout` |
| Equipment | `placer:start`, `placer:confirm`, `placement:pickup` |
| Crafting | `craft:list`, `craft:start`, `craft:run`, `craft:finish`, `craft:cancel` |
| Optional application | `product:start`, `product:finish`, `product:cancel` |
| Selling | `sell:products`, `sell:start`, `sell:complete`, `thief:loot` |
| Rewards and bag | `goals:claim`, `awards:claim`, `bag:list`, `bag:use` |

The server determines catalogue prices, recipe requirements, random sale outcomes and reward amounts. Craft/application completion uses server timing, and claim state prevents repeated claims. Thief recovery must follow completed sale resolution; buyer AI and downed-state presentation remain client-driven.

## Interface Contract

The Lua client sends these interface action groups:

- Initialization/windows: `init`, `openShop`, `openCrafting`, `openStats`, `openGoals`, `openAwards`, `openLeaderboard`, `openBag`, `openPicker`, `hubData`, `closeAll`
- Placement/START: `openPlacer`, `placerUpdate`, `closePlacer`, `startHint`, `startHintHide`
- HUD: `notify`, `progress`, `progressEnd`, `xpGain`, `rankUp`, `awardUnlocked`

The interface posts to these NUI callbacks: `uiReady`, `close`, `shopCheckout`, `craft`, `place`, `sell`, `useProduct`, `claim`, `pickerSelect`, `hubTab`, `bagUse`, `placerAction`.

The callbacks live in the Lua client. Window/cancel/readiness callbacks handle local state; gameplay callbacks request the server. Shop and claim responses update the interface only after successful server replies. A craft callback starts a separate timed in-game flow; its initial response is not proof of crafting completion.

## Local Startup Events

`gs-glowup:server:ready` and `gs-glowup:client:ready` are local initialization events used by the resource. `gs-glowup:client:cleanup` tears down props and presentation on stop. They are not exported gameplay controls. Do not use network events to bypass the normal inventory, model, ownership or request checks.
