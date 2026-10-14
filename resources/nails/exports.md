# API & Exports

## Client Exports

These exports are for other client resources. They read local state and are not server authorization checks.

| Export | Returns / effect |
|---|---|
| `IsSittingInChair()` | Boolean: local player occupies a customer chair |
| `GetSittingChairKey()` | Customer chair id or nil |
| `IsSittingAtWorkerStation()` | Boolean: local player occupies a worker stool |
| `GetWorkerStationKey()` | Worker station id or nil |
| `IsSalonSessionActive()` | Boolean: local player is in an active service session |
| `CanLeaveSalonChair()` | Boolean: seated without an active session/stand-up already in progress |
| `StandUp()` | Requests normal local chair exit when allowed |
| `IsDoingNailGesture()` | Boolean: inspect/show gesture is active |
| `StopNailGesture()` | Cancels an active nail gesture |

```lua
if exports.gs_nails:IsSalonSessionActive() then
    return -- Delay your activity while the manicure session is active.
end

exports.gs_nails:StopNailGesture()
if exports.gs_nails:CanLeaveSalonChair() then
    exports.gs_nails:StandUp()
end
```

## Server Media Exports

The server JavaScript bundle registers seven media exports used by the internal Lua adapter: `gsNailsMediaUpload`, `gsNailsMediaStart`, `gsNailsMediaPart`, `gsNailsMediaFinish`, `gsNailsMediaAbort`, `gsNailsMediaRefreshDiscord` and `gsNailsFetchRemoteImage`. They are internal interfaces and are not public player endpoints. Their results include `ok` and provider/error data. Keep provider credentials on the server.

There is no public export to grant a free set, create currency or approve a customer's payment. Resource events/callbacks validate identity, jobs, ownership and session participation internally. Integrate through the editable bridges instead of emitting internal payment events from unrelated resources.

## State and Persistence

| Data | Location |
|---|---|
| Worn selection | Qbox player metadata under `Config.MetadataKey` |
| Worn descriptor | Replicated player state under the same key |
| Station occupancy | Global state `gsn_station:<stationId>` |
| Custom slot lease | Global state `gsn:<gender-letter><drawable>:<texture>` |
| Saved design/layers/image | SQL `gs_nails_designs` |

Read replicated state for presentation if needed. Treat it as resource-owned state; modifying it bypasses the normal appearance/lease lifecycle and is unsupported. Session chairs and texture leases are transient and rebuilt at runtime, while SQL designs and character metadata persist.
