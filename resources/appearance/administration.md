# Administration

`/gsadmin` opens the admin dashboard: an interactive map of every store, a store inspector, the item-rule editor, and the server-wide accent color.

The command name is `Config.AdminCommand`. Admin is granted by an identifier in `Config.Admins`, the `gs_appearance.admin` ACE, a Qbox or QBCore permission from `Config.QBPermissions`, or an ESX group from `Config.ESXGroups` — see [Configuration](configuration.md#admin-access).

{% hint style="info" %}
Every admin action is validated on the server before it takes effect. The client-side admin check only decides whether the dashboard offers the controls.
{% endhint %}

---

## Stores

Store zones live in `gs_appearance_stores`, not in a config file. Fifteen clothing stores, seven barbers, and six tattoo parlors are seeded on first run; everything after that is done here.

### The map

The dashboard shows a GTA V map with a marker for every store, color-coded by type. Selecting a marker — or a row in the table beside it — opens that store in the inspector.

### Store fields

| Field | Description |
|---|---|
| **Name** | Shown in the editor's subtitle while the shop is open |
| **Type** | `clothing`, `barber`, `tattoo`, or `surgeon` |
| **Coordinates** | X, Y, Z. **Use my position** captures where you are standing |
| **Heading** | Facing used when framing the ped |
| **Radius** | Interaction radius in meters, clamped to 1.0–25.0 |
| **Enabled** | A disabled store keeps its row but has no prompt and no blip |
| **Jobs** | Job or gang locks, each with a minimum grade. Empty means public |
| **Blip** | On or off, plus sprite, color, and scale overrides |

Leaving Z at zero when creating a store snaps it to the ground under the given X and Y.

### Job and gang locks

A store's `jobs` list is a set of `{ name, minGrade }` entries. A player passes if their **job or gang** matches a listed name at or above that grade, so one entry covers both. An empty list makes the store public.

Job options are read live from the framework, so the list matches whatever jobs the server actually has.

### Creating a shop type that is not seeded

Surgeon shops are not seeded. Stand where you want one, create a store, set its type to `surgeon`, and use **Use my position**.

{% hint style="warning" %}
A store zone is a database row shared by everyone. Deleting one removes it for the whole server. Disable a store instead of deleting it if you may want it back.
{% endhint %}

---

## Item Rules

Item rules restrict individual clothing and prop drawables. They are stored in `gs_appearance_item_rules`, enforced on the server, and used client-side to filter what the carousel even shows.

Each rule targets one **category** and one **drawable index**, and has one of three types:

| Type | Effect |
|---|---|
| `blacklist` | Nobody may use the item |
| `job_lock` | Only players holding one of the listed jobs or gangs at the required grade may use it |
| `player_whitelist` | The listed identifiers may use the item |

### Precedence

When several rules target the same item, they are resolved in this order:

1. **Player whitelist wins.** A player on a whitelist for that item may use it regardless of any other rule.
2. **Job locks are then required.** If any `job_lock` rule exists on the item, the player must match at least one of them or the item is denied.
3. **Blacklist denies.** If the item is still allowed and a `blacklist` rule exists, it is denied.

An item with no rules is always allowed.

This means a `job_lock` alone is enough to make an item police-only — you do not also need a blacklist. It also means a whitelist is a genuine override, useful for granting one person an otherwise blacklisted item.

### Fields

| Field | Used by | Description |
|---|---|---|
| **Category** | All | The clothing or prop category, e.g. `tops`, `masks`, `hats` |
| **Drawable** | All | The item's drawable index |
| **Jobs** | `job_lock` | Job or gang names with a minimum grade |
| **Identifiers** | `player_whitelist` | Player identifiers, e.g. `license:…` |
| **Note** | All | Free text for your own reference |

A `job_lock` with no jobs and a `player_whitelist` with no identifiers are rejected, because both would silently lock the item for everyone.

Rules take effect immediately: the updated list is pushed to every connected player as soon as it is saved, with each player receiving their own unlock set.

---

## Accent Color

The dashboard has a color control for the UI accent. Changing it:

- persists server-side, so it survives restarts
- pushes to every connected player immediately
- overrides `Config.AccentColor`, which is only the initial value

Only a valid `#rrggbb` value is accepted, and only from an admin.

---

## Diagnostics

`/gsdiag` answers "why is this player's appearance or outfit not saving?" in one command. Run it in game as the affected player, or from the console against a player id:

```text
gsdiag 12
```

It reports:

| Line | Meaning |
|---|---|
| `framework` | The adapter that was detected |
| `oxmysql` | The resource state of `oxmysql` |
| `citizenid` | The identity the server resolves for that player |
| `playerskins` | Row count, and how many are active |
| `player_outfits` | Row count |
| `creator opens` | Whether that player would be sent to the character creator |
| `outfit write` | The result of a real write-and-delete round trip |

An unresolved `citizenid` is the definitive answer to most save problems — nothing can save until it returns a value.

The command is deliberately not ACE-gated: it only ever touches the caller's own rows plus one temporary row that is deleted immediately, and its whole purpose is to still work when admin detection is itself misbehaving. In game it self-targets and has a ten-second cooldown.

---

## Job and Gang Uniforms

Uniforms live in `management_outfits` and are managed by whichever job or boss menu your server already uses. GS Appearance exposes the illenium-compatible events for saving and deleting them:

```lua
TriggerServerEvent('illenium-appearance:server:saveManagementOutfit', outfit)
TriggerServerEvent('illenium-appearance:server:deleteManagementOutfit', id)
```

Both are permission-checked on the server against the job the outfit belongs to, so a player can only manage uniforms for a job they actually run. See [API & Exports](exports.md#outfit-and-uniform-events).

Rows can also be inserted directly:

| Column | Purpose |
|---|---|
| `job_name` | Job or gang name |
| `type` | `Job` or `Gang` |
| `minrank` | Minimum grade that sees the uniform |
| `name` | Display name |
| `gender` | `male` or `female` |
| `model` | Ped model the outfit was built on |
| `components`, `props` | The outfit itself, as JSON |
