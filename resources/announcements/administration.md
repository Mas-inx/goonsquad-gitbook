# Administration

---

## Access Levels

GS Announcements has three separate access layers:

| Layer | Purpose | Stored In |
|-------|---------|-----------|
| Administrator access | Full dashboard and immediate publishing | `Config.Admins`, ACE, or a super-admin whitelist entry |
| Builder whitelist | Allows a regular player or job to submit announcements | MySQL Access Control entries |
| Content rules | Limits individual layouts, effects, or lightshows | MySQL Access Control rules |

All important checks are repeated on the server. Hiding controls in the NUI is not the security boundary.

---

## Whitelisting Jobs and Players

Open **Manage → Access Control**.

### Job Entry

Use the framework's internal job name, such as `police` or `burgershot`. A job entry can include a minimum grade. Players below that grade do not match the entry.

### Player Entry

Use a stable identifier, preferably `license:`. Players can run `/gsid`, or an admin can choose an online player from the Access Control roster.

### Access Tiers

| Tier | Job | Player | Behavior |
|------|-----|--------|----------|
| Permanent | Yes | Yes | Builder access until the entry is removed |
| One-time | Yes | Yes | Entry is consumed by the first successful publish — the submission to Approvals, or the direct broadcast when approval is disabled |
| Super admin | No | Yes | Full admin dashboard and immediate publishing |

Super-admin access is managed live in MySQL and does not require a resource restart.

{% hint style="warning" %}
A one-time job entry is shared by everyone matching that job and grade. The first matching player's successful submission consumes the entry.
{% endhint %}

---

## Restricting Premade Content

The Access Control page can restrict:

- Layouts
- Animated background presets
- Ready-made lightshow presets

Assign allowed jobs and/or individual players to a content item. An empty rule means the item is available to everyone. Admins always bypass content restrictions.

The limited Builder hides restricted choices, and the server rejects a crafted publish payload that still tries to use them.

---

## Reviewing Player Announcements

The approval queue is used while `Config.PlayerMode.RequireApproval = true` (the default). With it set to `false`, player publishes skip the queue and broadcast or schedule directly.

Open **Manage → Approvals**. Pending submissions include the submitter, business, tagline, saved design, and any requested schedule.

### Approve

- An immediate submission broadcasts to all connected players.
- A scheduled submission is inserted into the scheduler with its requested repeat mode.
- The row remains visible as approved until an admin clears it.

### Reject

Add an optional note of up to 255 characters. The player sees the rejected state and note in the Builder. The row remains visible until cleared.

Pending rows cannot be deleted directly; approve or reject them first.

---

## Reviewing Assets

Admins and whitelisted players submit media into their own libraries; every submission lands as pending. By default, every admin can review media. To create a narrower reviewer tier, add identifiers to `Config.AssetApprovers`.

On **Asset Library → Review**:

1. Inspect the name, owner, type, and direct URL.
2. Approve safe, stable media or reject it with a note.
3. Approved media becomes selectable according to `Config.Assets.ShareApproved`.

The resource validates the URL scheme but cannot guarantee that a remote host will remain available. Prefer self-hosted files for permanent assets.

---

## Business Status

The Business Status page is seeded from `Config.Businesses`. Admins can switch each business between its available states. Changes are saved to MySQL and emit the server-side `gs:server:businessStatusChanged` event for map, phone, or dispatch integrations.

---

## Data Storage

With the default prefix, the resource owns these tables:

| Table | Purpose |
|-------|---------|
| `gs_announce_profiles` | Reusable announcement designs and profile state |
| `gs_announce_history` | Broadcast records and resend snapshots |
| `gs_announce_business_status` | Current business states |
| `gs_announce_scheduled` | Active and completed schedule rows |
| `gs_announce_prefs` | Global player-facing preferences |
| `gs_announce_meta` | Internal migration markers |
| `gs_announce_events` | Deduplicated GPS and action engagement |
| `gs_announce_assets` | Media submissions and review state |
| `gs_announce_pending` | Player submissions and review state |
| `gs_announce_access` | Job/player whitelist entries |
| `gs_announce_content_rules` | Layout, background, and lightshow restrictions |

Treat these tables as resource-owned. Back them up normally, but use the panel and supported commands for changes.

---

## Legacy JSON Migration

On the first successful database start, the resource checks the paths under `Config.Storage`, imports any compatible JSON data, and writes a `json_imported` marker to the meta table.

The old JSON files are not modified or used for ongoing persistence. After confirming the MySQL data, they may be archived or removed.

To intentionally repeat the import, stop the resource, back up the database, remove the `json_imported` row from `gs_announce_meta`, and restart. Re-importing without a plan can duplicate or overwrite data.

---

## Operational Checks

Use these commands before requesting support:

```text
gsannounce:admins
gsannounce:whitelist
gsannounce:db
gsannounce:analytics 7d
```

They report the effective access configuration, database health, current row counts, and the same analytics values sent to the NUI.
