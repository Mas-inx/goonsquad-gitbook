# Usage

## Enter the Lounge

Use `/baddiehub` to open or close the hub. Your character must pass the configured access gate. An optional keybind can be enabled by staff. On first entry, dismiss the welcome screen and personalize your profile. Escape dismisses the active modal or welcome screen first, then closes the hub.

## Profiles, Friends and Messages

Use your profile page to set a unique display name, bio and avatar. Choose an included portrait, item icon or permitted HTTPS image. Theme and language preferences are saved per profile.

Search for a member with at least two characters of their display name. Send a friend request; the recipient can accept or decline. Online indicators update while eligible sessions are active. Open a friend's message thread to chat privately. DMs are friends-only by default; opening history marks messages read. Messages persist while the recipient is offline.

Blocking severs friendship and hides the blocked relationship from chat, member search, conversations and live presence. Both directions are checked for messaging. Removing a block does not restore friendship. Members can delete their own lounge messages; moderators can delete anyone's. Deletion hides the message and retains an audit record in SQL.

## Claim Starter Rewards

The Starter Claims page contains the welcome pack and, when available, the starter vehicle. Default pack: $2,500 cash and one Welcome Box. Use the box in your inventory to receive its configured contents. Starter pack and car are one-time claims per account by default, including other characters on that account.

Claim the car outdoors with room nearby. Ownership is written before spawning. If spawning fails, the framework vehicle is marked stored and staff should verify garage retrieval. Keys depend on the selected integration; no key resource leaves doors unlocked. Standalone ownership requires a custom garage integration for retrieval.

If a bundle is only partly delivered, the claim remains recorded to prevent duplicate money or items. Staff can inspect console/claim-webhook details and supply only missing rewards. A failed box grants back the box only when no contents could be delivered. Verify adapter behavior before opening claims to players.

## Season Pass

Earn XP from daily membership login, eligible lounge messages and Lounge Pass items. The day changes according to server time. Chat XP has a minimum message length and a rolling cap. Claim each unlocked reward separately on the Season Pass page. Failed deliveries release that reward for retry. Starter-claim resets do not reset the season.

## Discover the City

Browse business listings, events and City Info. GPS buttons set a waypoint when a listing includes coordinates. These listings describe content managed by your server; they do not book services, grant jobs or start activities.

## Commands and Permissions

| Command | Access | Purpose |
|---|---|---|
| `/baddiehub` | Players; gate applies | Toggle the lounge |
| `/fhgender <id> female|male|clear` | Console, admin ACE or `command.fhgender` | Override the online character's gender gate |
| `/fhresetclaims <id>` | Console, admin ACE or `command.fhresetclaims` | Reset the online player's starter pack and vehicle claims |

Admin ACE defaults to `gs_baddiehub.admin`. Framework moderator groups do not automatically grant these administrative commands. The target must be online and loaded. Gender overrides affect the gender component; they do not bypass a Discord-only or combined role requirement. Claim resets do not revoke money, items or already owned cars; use them only for an intentional reissue. These are the only three registered commands in the release.
