# Troubleshooting

Start with the resource's built-in diagnostics:

```text
gsannounce:admins
gsannounce:whitelist
gsannounce:db
gsannounce:analytics 7d
```

---

## Installation and Database

### `oxmysql is not started`

Place oxmysql before GS Announcements in `server.cfg`:

```cfg
ensure oxmysql
ensure gs-annoucements
```

Confirm the connection string is configured and oxmysql connected to the intended database before this resource starts.

### Database setup failed or lists stay empty

- Run `gsannounce:db` and read the first database error in the server console.
- Confirm the database user can create and alter tables.
- Confirm `Config.Database.TablePrefix` contains only letters, numbers, and underscores.
- Do not manually import a partial schema; the resource creates all tables automatically.

### Legacy JSON files still exist

This is expected. They are imported once and left untouched as a backup. `gsannounce:db` reports whether old files remain on disk.

If old data did not import, confirm the files match the paths in `Config.Storage`. Only deliberately remove the `json_imported` meta row after taking a database backup.

---

## Panel and Permissions

### `/gsannounce` or F6 does nothing

- Run `/gsid` and compare the printed license with `Config.Admins`.
- Run `gsannounce:check <playerId>` while the player is online.
- Restart the resource after changing `config.lua`.
- If `Config.NotifyOnDenied = false`, denied users receive no message by design.
- If the user is a regular player, confirm player mode is enabled and the whitelist allows their job or identifier.

### Admin is treated as a limited player

- Confirm `Config.PermissionMode` is `identifiers` or `ace` as intended.
- Confirm the exact ACE node matches `Config.AcePermission`.
- Run `gsannounce:admins` to see which online players actually match.
- Check for identifier typos, especially `license:` versus `identifier.license:` syntax in `server.cfg`.

### Player cannot publish

- Confirm the selected framework started before GS Announcements.
- Check `Config.PlayerMode.JobProvider` and the player's internal job name.
- Confirm their job grade meets the Access Control entry's minimum grade.
- With `RequireApproval = true`, confirm they have fewer than `Config.PlayerMode.MaxPending` pending submissions.
- Run `gsannounce:whitelist` to inspect effective entries and content rules.

### One-time access disappeared

One-time access is consumed by the player's first successful publish — the submission to Approvals, or the direct broadcast when `Config.PlayerMode.RequireApproval = false`. Add a new one-time or permanent entry from Access Control.

---

## NUI and Overlay

### Dashboard is blank or the UI does not load

The resource loads `web/dist/index.html`. Restore the shipped build or rebuild it:

```bash
cd web
npm install
npm run build
```

Restart `gs-annoucements` afterward.

### `SEND_NUI_MESSAGE: invalid JSON passed in frame`

The client sanitizes and queues NUI messages until the page is ready. Capture the complete F8 line naming the failed action and check for local modifications that send non-finite numbers, functions, or unsupported values.

### Mouse cursor keeps the wrong shape

The resource nudges the cursor when the panel opens and resets cursor state in the web app. Remove custom `cursor-*` classes from elements whose state changes while the panel is hidden.

### Announcement has a black box behind it

Do not add CSS `filter` or `backdrop-filter` to announcement-card elements. FiveM CEF can composite these against an opaque surface instead of the game view. Use gradients and box shadows for depth.

---

## Media and Asset Approval

### Image, GIF, or video does not appear

- Use a direct file URL, not a gallery or share page.
- Use `https://`; the NUI blocks insecure HTTP media.
- Avoid expiring Discord CDN links.
- Check that the host allows hotlinking.
- For reliability, place the file in `web/public/media`, rebuild, and use `media/filename.ext`.

### Media works in preview but Publish is denied

The URL is not approved for the current user. Submit the exact URL in the Asset Library and wait for approval. When `ShareApproved = false`, another user's approval does not grant access to you.

### Approved media disappears from a resend or schedule

The asset may have been deleted or its URL no longer matches an approved row. The server revalidates stored snapshots at send time and removes unapproved media.

### Embedded data URL is rejected by the Asset Library

This is intentional. Embedded data cannot be reviewed as a stable shared asset. Use a direct HTTPS or self-hosted resource URL.

---

## Scheduling, GPS, and Analytics

### Scheduled announcement fires at the wrong time

- Confirm the server operating system clock and timezone.
- Confirm the Builder timestamp is correct.
- Allow up to `Config.SchedulerInterval` seconds after the due time.
- For recurring schedules, remember that daily and weekly repeats advance by fixed day intervals.

### No waypoint prompt appears

- Enable GPS on the announcement.
- Pick a waypoint in the Builder or match the business name exactly with `Config.Businesses`.
- Confirm the configured coordinates contain numeric `x`, `y`, and `z` values.

### Analytics is all zero

Publish a new broadcast and interact with it. Historical GPS presses from versions that did not send engagement events cannot be reconstructed.

Run:

```text
gsannounce:analytics 24h
```

If the console values are correct but the page is not, capture the NUI/F8 error. If both are empty, verify history and event row counts with `gsannounce:db`.

### GPS count does not increase repeatedly

This is expected. GPS and action events are deduplicated per player, event kind, and announcement. Repeated clicks by the same player count once.

---

## Information to Send Support

- GS Announcements version
- Framework and framework version
- Output of `gsannounce:admins`
- Output of `gsannounce:whitelist`
- Output of `gsannounce:db`
- Relevant server-console and player F8 errors
- Exact steps that reproduce the problem
- Whether `web/src`, `config.lua`, or the resource folder name was changed

Remove licenses, Discord IDs, database credentials, connection strings, and private media URLs before sharing logs publicly.
