# Usage

---

## Player Guide

### Opening the Ledger

Press **F7** or run `/playerrewards` to open the Operator Ledger.

The ledger shows:
- Your **Operator Card** with playtime stats, tier, and Prime status
- **Season Bar** with current season name and tag
- **Free Track** — rewards available to all players
- **Prime Track** — rewards unlocked with Prime subscription

### Earning Rewards

1. Play on the server to accumulate hours.
2. Each tier requires a certain number of hours to unlock.
3. Open the ledger to see which tiers you've unlocked.
4. Click a reward to view details and claim it.

### Claim All

Click **Claim All** to claim every eligible unclaimed reward at once.

### Prime

Prime gives access to the premium reward track. Obtain Prime via:

- **Tebex** — purchase from the store (if configured)
- **In-game** — buy with cash or bank (if Tebex is disabled)
- **Discord** — auto-granted from Discord role (if configured)
- **Admin** — granted by server staff

---

## Overseer Admin Panel

Open the ledger and click **Overseer** (top-right) to access the admin panel. Requires ACE permission `playerrewards.admin` or matching `Config.AdminIdentifiers`.

### Reward Tiers Tab

- Separate sections for **Free** and **Prime** rewards
- **Add Reward** — create a new reward with type, payload, hours required, and image
- **Edit** — modify existing rewards
- **Delete** — remove rewards
- **Reorder** — use **▲ / ▼** arrows to change order
- **Push live** — saves to MySQL and broadcasts to all open ledgers

### Reward Types

| Type | Payload | Fields |
|------|---------|--------|
| Cash | Amount | `amount` |
| Item | Item name | `payload`, `amount` |
| Weapon | Weapon name | `payload` (e.g. WEAPON_PISTOL) |
| Vehicle | Model name | `payload` (e.g. adder) |
| Clothing | Item name | `payload` (outfit metadata) |
| XP | Amount | `amount` (notification only) |
| Custom | Label | `label`, optional `amount` |

### Season + Relay Tab

- **Season Name** — displayed in ledger header
- **Season Tag** — short identifier (used in claim audit log)
- **Total Tiers** — number of tiers on each track

#### Tebex Relay
- **Tebex Enabled** — toggle store checkout mode
- **Tebex URL** — Tebex package checkout URL
- **Button Label** — text shown on the Get Prime button

#### In-Game Prime
- **Prime Price** — cost in cash/bank (when Tebex is off)

### Unsaved Changes

If you close Overseer with local edits not yet pushed live, a warning notice appears. You can reopen or push live.

---

## Admin Commands

| Command | Args | Description | Permission |
|---------|------|-------------|------------|
| `/playerrewards` | — | Open ledger | None |
| `/granthours <id> <hours>` | server_id, hours | Add playtime hours | ACE `playerrewards.admin` |
| `/grantprime <id>` | server_id | Grant Prime | ACE `playerrewards.admin` |
| `/removeprime <id>` | server_id | Remove Prime (in-game mode only) | ACE `playerrewards.admin` |
| `playerrewards_resetdefaults` | — | Reset season to defaults | Server console only |

---

## Prime System Flows

### Tebex Mode (tebexEnabled = true)

1. Player clicks **Get Prime** in ledger.
2. Server checks Discord is configured and player has the role.
3. If valid, Tebex checkout URL opens in browser.
4. After purchase, a Tebex package command calls `exports['gs-playerrewards']:GrantPrime(source)`.
5. Player re-opens ledger to see Prime activated.

### In-Game Mode (tebexEnabled = false)

1. Player clicks **Get Prime** in ledger.
2. Purchase modal opens with Cash and Bank options.
3. Player selects payment method and confirms.
4. Money is deducted and Prime is granted immediately.

### Discord Auto-Grant

If Discord is configured with `AutoGrantFromRole = true`:
- Every time the ledger opens, the server checks the player's Discord roles.
- If they have the configured `PrimeRoleId`, `hasPrime` is set automatically.

---

## Data Storage

All season data, player progress, and settings are stored in MySQL:

| Table | Purpose |
|-------|---------|
| `playerrewards_state` | Season JSON, Tebex URL, Prime settings |
| `playerrewards_players` | Per-player hours, Prime flag, claimed rewards |
| `playerrewards_claims` | Claim audit log |
| `playerrewards_sessions` | Session history chunks |

Tables are auto-created on first start by `server/db.lua`.
