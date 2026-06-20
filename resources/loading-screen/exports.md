# API & Exports

---

## Client Exports

```lua
exports['gs_loadingscreen']:SendQueueStatus(payload)
```
Update the queue status widget. `payload = { position, total, estimatedWait?, priority? }`

```lua
exports['gs_loadingscreen']:SetActiveLoadingConfig(config)
```
Programmatically set a loading config (table).

```lua
exports['gs_loadingscreen']:MarkLoadingComplete()
```
Manually mark loading as complete (useful for custom loading flows).

```lua
exports['gs_loadingscreen']:OpenBuilder()
exports['gs_loadingscreen']:CloseBuilder()
exports['gs_loadingscreen']:IsBuilderOpen()
```

```lua
exports['gs_loadingscreen']:OpenLoadingPreview()
exports['gs_loadingscreen']:CloseLoadingPreview()
exports['gs_loadingscreen']:IsLoadingPreviewOpen()
```

---

## Server Exports

```lua
exports['gs_loadingscreen']:GetActiveConfig()
```
Returns the currently published config table.

```lua
exports['gs_loadingscreen']:PublishConfig(identifier)
```
Publish a profile by its identifier slug.

```lua
exports['gs_loadingscreen']:IsPlayerAdmin(source)
```
Check if a player has builder access.
