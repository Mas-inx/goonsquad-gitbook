# Usage

## Start Your Career

1. Use an accepted creator model. By default this is `mp_f_freemode_01`.
2. Visit the **OnlyShots Boutique** near `-608.64, -1130.93, 22.27` and buy the $10,000 camera through the target option or E prompt.
3. Open the app with `/onlyshots` or F7. Start the camera from **Create hub**, use the camera item, or type `/camera`.
4. Choose a shot and pose, capture with E or left click, and save the caption dialog.
5. Review your photo in **My content**, combine several into a collection, or call a buyer from **Market**.

The model and optional Discord role restrictions apply to creator actions. Players can open the app and buy collectibles without being creators. No framework job is required.

---

## Commands and Keybinds

| Command | Action |
|---|---|
| `/onlyshots` | Open the full app |
| `/sellshots` | Call a buyer for the newest creator-owned collection, otherwise newest photo |
| `/sellshots photo` | Prefer the newest creator-owned single photo |
| `/camera` | Open photo mode if you own a camera |
| `/stats` | Open career statistics |
| `/top` | Open the leaderboard |
| `/goals` | Open daily and weekly goals |
| `/awards` | Open achievements |

These names can be changed or disabled in `Config.Commands`.

| Key | Action |
|---|---|
| F7 | Toggle the app; default `Config.OpenKey` |
| H while free-aiming at a player | Send a purchase offer; default `Config.Trade.key` |
| 1–9 in the app | Switch pages in sidebar order |
| L in the app | Cycle interface language |
| Esc | Close the active popup, viewer or app |
| Left/right in the viewer | Browse collection photos |
| Y or Enter on a trade offer | Accept |
| N or Esc on a trade offer | Decline |

Studio and trade mappings can be rebound in FiveM key settings. Their mapped command names are `gs_studio` and `gs_tradeoffer`; disabling a configurable chat command does not remove those mappings. App shortcuts do not navigate while typing in an input.

---

## The App

| Page | What You Can Do |
|---|---|
| Home | View rank progress, recent captures, daily goals and career highlights |
| My content | Search captures, filter by type/hotspot/collected status, sort and switch grid/list views |
| Collections | View collection decks and use the workbench to combine photos |
| Create hub | Start the camera, inspect shot types and poses, and set hotspot waypoints |
| Market | Choose an item, inspect sale odds and estimated payout, and call an NPC buyer |
| Goals | Track daily/weekly progress, reset timers and claim completed rewards |
| Achievements | View permanent milestone progress and unlocked rewards |
| Leaderboard | View top earners, your standing and the refresh countdown |
| Stats | View earnings, sales, followers, outcomes, discoveries and rank perks |

Use the language button for English, Spanish, German, French or Brazilian Portuguese. Changing language updates the interface and client notifications for the current session; it is not a saved account preference. The activity bell shows the current interface session's sale history.

---

## Photo Mode

| Control | Action |
|---|---|
| Mouse | Orbit the selfie subject or aim the point-of-view camera |
| Scroll wheel | Zoom in/out |
| Up/down | Cycle face, chest, full body, rear and feet presets |
| Left/right | Cycle the twelve poses |
| F | Switch between selfie and point-of-view modes |
| G | Open camera settings |
| E or left click | Capture |
| Backspace or Esc | Exit photo mode |

In the G settings menu, left/right selects **zoom**, **height** or **type**. Up/down adjusts the selected setting. Backspace or G closes settings; Esc exits photo mode. The rear body preset and the point-of-view camera mode are separate choices.

The default zoom range is 15–115 degrees and captures have a three-second cooldown. Vehicle camera use is disabled by default.

Capturing hides the camera interface, takes a screenshot when available, resizes it, and opens the caption dialog. Uploading can finish while you type. **Save** or Enter saves the capture; **Cancel** or Esc in the caption input discards it. A caption is optional and adds 5% to the stored value by default. Keep the camera open until the photo-save notification arrives.

Without a capture resource or ready image host, the photo still has value and gameplay metadata and uses photo-type artwork. See [Image Storage](configuration.md#image-storage) for persistence and deletion rules.

---

## Hotspots

Use **Create hub** to see the premium locations, required ranks and multipliers. Set a waypoint to an unlocked location, stand within its radius and take a photo. Your first saved photo at each eligible hotspot grants discovery XP.

The bonus is attached to the photo when it is saved. Your location at the time of sale does not change it. Captures outside a hotspot, or below its rank requirement, use a 1.00 location multiplier. Rank and hotspot bonuses stack.

Hotspot blips show only for accepted creator models by default. Rank-locked hotspots may still appear on the map; the bonus remains locked until the required rank.

---

## Make a Collection

In **My content**, choose **Select for bundle** and select your own photos, or open the **Collections** workbench and add photos through its picker. The projected payout updates as you select them.

The default minimum is three photos. Your current rank allows three to ten photos per collection. Collected photos from another creator cannot be included, and existing collections cannot be nested.

Press **Create bundle** once the selection is valid. The selected singles are consumed and replaced with one collection item containing their image references and captions. If item creation fails, the resource attempts to return the original singles.

Collection value receives a 1.45 base multiplier plus a 4% size bonus for each photo above the minimum. Its hotspot multiplier is the average of the included photo multipliers. Collections can be viewed photo by photo or offered to buyers.

---

## Sell to an NPC

Choose **Market**, select a photo/collection or leave the automatic choice, then call a buyer. Calling from an individual card selects that specific item. The app closes while the NPC walks to you.

Stay nearby and let the buyer arrive. The defaults are:

| Outcome | Chance | Result |
|---|---:|---|
| Success | 75% | Item is consumed and you receive the server-calculated payout |
| Rejection | 15% | You keep the item and lose a small number of followers |
| Theft | 10% | The NPC takes the item and runs; recover it before the timer ends |

A successful sale has a 20% chance of a $100–$600 tip and a 4% chance of an 8× viral price. Viral sales also grant extra followers and XP. Displayed estimates do not include random tips or viral outcomes.

The default cooldown is 45 seconds from the buyer call. Another buyer cannot be called while a sale or theft recovery is active. Moving too far away, dying before arrival, or an unresolved request timing out cancels the sale.

Only the original creator can sell a photo or collection to NPCs. Photos bought from other players appear as collected and cannot be sold this way.

### Recover Stolen Work

Follow the marked thief and kill the NPC before the default 60-second chase ends. Search the body through the target interaction or E prompt. The search takes four seconds by default and checks the thief's death and your distance on the server.

Complete the recovery promptly; there is only a short grace period for looting after the chase window. If your inventory cannot accept the recovered item, make room and retry before recovery expires. Escaped items are lost and their SQL images are deleted. Successful recovery returns the item and grants recovery XP.

---

## Buy Another Player's Work

Close the app, free-aim at a nearby creator carrying their own photo or collection, and press H. You must be within six metres and have enough money in the configured account.

The seller receives a timed offer containing buyer name, price, a blurred preview and accept/decline buttons. The server prefers their newest creator-owned collection, otherwise their newest photo. The default offer lasts 30 seconds.

On acceptance the server rechecks range, ownership, price, money and item transfer. The purchase adds collector metadata while retaining the original creator. View it from the inventory item or the app's collected content. Player trades transfer money directly and do not add NPC-sale XP, followers or leaderboard earnings.

---

## Goals, Achievements and Ranks

Photos, sales, eligible hotspot captures, followers and recoveries advance active challenges. Single-photo sales and collection sales count toward different goal types. Visit **Goals** and claim a completed challenge before its daily or weekly reset.

Everyone receives the same rotating challenge set. Daily resets are at 00:00 UTC and weekly resets are Monday at 00:00 UTC. The UI shows the remaining time.

Achievements award rewards automatically when lifetime milestones are reached. Rank increases unlock larger collections, better payout multipliers and access to higher-tier hotspots. The rank table in **Stats** shows the full progression.

The leaderboard ranks lifetime NPC-sale earnings, including tips and viral sales. Challenge rewards, achievement rewards and player trades do not count toward that earnings total.
