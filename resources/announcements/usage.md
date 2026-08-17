# Usage

---

## Opening the Panel

Run `/gsannounce` or press **F6**. Run the command again or press **Esc** to close it.

Admins see the full suite. Regular players see the Dashboard and a limited Builder when player mode and their access rules allow it.

---

## Creating an Announcement

1. Open **Create → Builder**.
2. Set the business name, tagline, message, and category.
3. Choose a layout and entrance animation.
4. Configure colors, typography, background effects, media, lightshow, size, and sound.
5. Enable GPS and either select a configured business or click **Set waypoint on map**.
6. Add or remove action buttons as needed.
7. Choose immediate publishing or enable a schedule.
8. Preview the card and click **Publish**.

Admins publish immediately. Limited players submit the same design to **Approvals** — unless `Config.PlayerMode.RequireApproval = false`, in which case their publish broadcasts or schedules directly and the Builder's buttons say so.

---

## Waypoints

The Builder resolves waypoint coordinates in this order:

1. Coordinates selected through **Set waypoint on map**
2. Coordinates from the matching `Config.Businesses` entry
3. No waypoint when neither source exists

When a broadcast contains coordinates, players can press **E** or use the card's waypoint action. A successful action sets the GTA waypoint, updates the card, and records one GPS engagement for that player and announcement.

---

## Layout, Background, and Lightshow Options

### Layouts

| Layout | Best Use |
|--------|----------|
| Cinematic Banner | Wide, high-impact general announcements |
| Modern Card | Compact information with a clear hierarchy |
| Minimal Toast | Short, quiet notifications |
| Neon Pulse | Nightlife and high-energy promotions |
| Split Showcase | Media alongside announcement copy |
| Breaking News | Urgent lower-third or news updates |
| Showcase Hero | Full-bleed artwork or video behind the content |

### Background Stack

Announcements render these layers in order:

```text
base color → media → text scrim → animated preset → lightshow
```

Use speed and intensity controls to keep text readable. Prefer WEBM or MP4 for short loops because they are usually smaller and smoother than GIFs.

### Lightshows

Ready-made options include police, sheriff, EMS, fire, taxi, tow, roadworks, government, nightclub, gold, and alarm styles. Custom lightshows support strobe, alternate, sweep, pulse, beacon, and chase patterns with up to three colors.

---

## Asset Library

All announcement media must be approved before it can be published. The Asset Library page is open to admins and to whitelisted players — everyone submits into their own library, and nothing becomes usable until an approver signs it off.

1. Open **Create → Asset Library**.
2. Submit a name and a direct HTTPS or `media/...` URL.
3. Wait for an asset approver to approve it.
4. Select the approved asset from the Builder's Background, Logo, or Artwork picker.

Players see their own submissions and statuses under **My library**; the **Review** tab appears only for approvers. Players can delete their own entries at any time.

```text
submit → pending review → approved → available in Builder
```

The server rechecks media when an announcement is published, resent, or fired by the scheduler. Unapproved media blocks an interactive publish and is removed from unattended resends or scheduled broadcasts.

Deleting an approved library entry makes its URL unapproved for future sends. Existing stored announcement snapshots still contain the URL, but the send-time check removes it.

---

## Scheduling

Scheduling is configured in the Builder before publishing.

| Repeat Mode | Behavior |
|-------------|----------|
| Once | Fires once, then deactivates |
| Daily | Advances by 24 hours after every send |
| Weekdays | Advances to the next Monday–Friday occurrence |
| Weekly | Advances by seven days |

The server checks due rows every `Config.SchedulerInterval` seconds. Schedule timestamps are interpreted by the server, so verify the server's clock and timezone.

---

## Profiles and History

### Profiles

Profiles hold reusable announcement designs. Admins can search, filter, duplicate, pause, delete, and open profiles in the Builder. Publishing upserts a profile using its business and tagline.

### History

Every broadcast creates a history row containing:

- Announcement and business names
- Layout and delivery channel
- Send time and player reach
- GPS engagement count
- Delivery status
- A full announcement snapshot for resend

Right-click a history row to resend it or duplicate it into a draft profile. Use **Export History** to write `data/history-export.csv` inside the resource.

---

## Analytics

Open **Overview → Analytics** and choose the last 24 hours, 7 days, or 30 days.

| Metric | Source |
|--------|--------|
| Total Reach | Sum of online-player reach stored on broadcast history |
| GPS Waypoints | Deduplicated `gps` engagement events |
| Broadcasts | History rows in the selected window |
| Action Clicks | Deduplicated non-GPS action events |
| Peak Audience | Highest reach for one broadcast |
| Best Hour | Average reach grouped by server-local hour |

Analytics is written when broadcasts or interactions happen and queried while the page is open. Engagement rows older than `Config.Analytics.RetentionDays` are pruned hourly.

---

## Commands

| Command | Access | Description |
|---------|--------|-------------|
| `/gsannounce` | Admin or eligible player | Open or close the panel |
| `/gsid` | Any in-game player | Print the player's identifiers in chat and the server console |
| `gsannounce:admins` | Console or admin | Show permission configuration and matched online admins |
| `gsannounce:whitelist` | Console or admin | Show whitelist entries and content rules |
| `gsannounce:check <playerId>` | Console or admin | Explain an online player's admin match and list identifiers |
| `gsannounce:testme` | In-game admin | Display a private test announcement |
| `gsannounce:broadcast <message>` | Console or admin | Send a basic server-wide announcement |
| `gsannounce:db` | Console or admin | Show database state, row counts, and legacy JSON status |
| `gsannounce:analytics [24h\|7d\|30d]` | Console or admin | Print the live analytics payload from MySQL |

---

## What Players See

The NUI overlay is transparent outside the announcement card. It reproduces the selected layout, colors, media, effects, lightshow, dimensions, animation, sound, and waypoint action over the game world.

Card scale and width are part of the broadcast, so all recipients see the dimensions selected by the publisher.
