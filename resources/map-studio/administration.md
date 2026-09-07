# Administration

---

## Access Levels

GS Map Studio has two access layers, both enforced on the server:

| Layer | Purpose | Granted By |
|-------|---------|------------|
| Builder access | Open the studio, create and edit maps, save prefabs, export, publish | `gsms.use` ace, `Config.Permissions.AllowedIdentifiers`, or `Config.Permissions.AllowedJobs` |
| Admin | Everything a builder can do, plus delete other builders' maps and prefabs, override another builder's lock, and run the reload command in game | `gsms.admin` ace |

Every privileged event (placing, deleting, world edit, save, rename, flags, recovery, export, publish) re-validates the caller's session and permission. Hiding controls in the NUI is not the security boundary.

```cfg
add_ace group.admin gsms.use allow
add_ace group.admin gsms.admin allow
```

A player who loses permission mid-session is closed out on their next privileged action.

---

## Map Ownership and Locks

Each map records its **author** (the creator's `license:` identifier) and, when locked, the **lockedBy** identifier.

| Action | Who may do it |
|--------|---------------|
| Edit, save, rename, change flags, resolve recovery | Any builder while the map is unlocked; only the locker (or admin) while locked |
| Delete a map | The author or an admin |
| Delete a prefab | The prefab's author or an admin |
| Lock or unlock | Any builder while unlocked; only the locker (or admin) while locked |

A builder who opened a map before someone else locked it drops to read-only immediately and cannot force-save over the locker. Deleting a map kicks every builder out of it and unloads it for all players.

{% hint style="warning" %}
Locks are stored on the map and persist across restarts. If a builder locks a map and leaves the community, an admin with `gsms.admin` must unlock it from the Maps panel.
{% endhint %}

---

## Map Flags

| Flag | Effect | Notes |
|------|--------|-------|
| **Locked** | Restricts all edits to the locker | Shown to other builders as **Read-only** |
| **Public** | Streams the map to every player on the server, not only builders | Turning it off hides the map from non-builders immediately |
| **Autoload** | Loads the map into the live state when the server starts | Combine with Public for a permanent world change |

Flag changes are written to `data/index.json` straight away, so they survive a restart even if the map itself has unsaved edits.

---

## Data Storage

Everything is stored as JSON inside the resource. There is no database.

| Path | Purpose |
|------|---------|
| `data/index.json` | Registry of maps and prefabs with light metadata (name, author, flags, counts, timestamps) |
| `data/maps/<id>.json` | Full map documents: objects, lights, hides, layers, groups |
| `data/autosave/<id>.json` | Rolling autosave, offered for recovery when newer than the last save |
| `data/prefabs/<id>.json` | Prefab (set) documents |
| `exports/` | Files written by **Export** |
| `published/<name>/` | Resources written by **Publish** |

Back up the `data/` folder like any other server data. To move maps between servers, copy `data/index.json` together with the `maps/` and `prefabs/` folders.

{% hint style="info" %}
FiveM has no file-delete API. Deleting a map or prefab removes it from the index and empties its document to zero bytes. The empty files are harmless and can be cleaned up manually.
{% endhint %}

### Autosave

The autosave loop runs every `Config.AutosaveInterval` seconds and writes every map with unsaved changes. Autosave also fires when the last builder leaves a map and when the resource stops cleanly. On boot the server compares each map's autosave time with its save time and prints how many maps have recovery available:

```
[gs-map-studio] 2 map(s) have unsaved autosaves; recovery is offered in the studio.
[gs-map-studio] storage ready: 14 map(s), 3 prefab(s).
[gs-map-studio] autoloaded 1 map(s).
```

Recovery is resolved per map from the Maps panel by a builder who has the map open and is not blocked by someone else's lock.

---

## Server-side Limits

Every mutation batch is validated field by field against a whitelist, then applied against these caps. Rejected operations trigger a resync so the builder's view converges with the server.

| Limit | Default | Behaviour when exceeded |
|-------|---------|-------------------------|
| `Config.MaxObjectsPerMap` | 6000 | New objects are refused |
| `Config.MaxLightsPerMap` | 2000 | New lights are refused |
| `Config.MaxHidesPerMap` | 4000 | New world-prop hides are refused |
| `Config.MaxLayersPerMap` | 128 | New layers are refused; the `default` layer can never be deleted |
| `Config.MaxGroupsPerMap` | 512 | New groups are refused |
| `Config.MaxOpsPerBatch` | 150 | The whole batch is rejected and the builder is resynced |
| `Config.OpsPerSecondLimit` | 600 | The batch is dropped without being charged, so a smaller batch in the same second still passes |
| Export / publish cooldown | 3 seconds per builder | The request is refused with a rate-limit toast |
| Prefab size | 512 objects and 512 lights | Save is refused |

Public runtime state requests from spectators are throttled to one every three seconds per player.

---

## Streaming and Performance

Map objects are client-local, non-networked entities. Each client spawns its own copies within `Config.StreamRadius` of the player, so nothing counts against the OneSync entity budget. Objects inside MLO interiors are assigned to the interior room automatically so they render correctly.

Lights are drawn every frame for players within `Config.Lights.DrawDistance`, capped at `Config.Lights.MaxPerScene` nearest lights. Shadow-casting lights are further limited by the engine.

---

## Console Command

```text
mapstudio_reload
```

Re-broadcasts the map and prefab indexes to every open studio session. Run it from the server console or in game with the `gsms.admin` ace. The command name follows `Config.Command`, so a custom command becomes `<command>_reload`. Changes to `config.lua` still need a resource restart, as with any Lua resource.

---

## Per-player Preferences

Favorites, collections, key bindings, theme, language, density, and category customizations are stored in each player's client KVPs. They are never sent to the server and are not part of a map. Resetting a player's studio preferences means clearing the resource's KVPs on that client.
