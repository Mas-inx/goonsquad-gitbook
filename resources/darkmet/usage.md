# Usage

---

## Player Guide

### Getting Started

1. Obtain a DarkMet Laptop from a dealer, crafting, or loot.
2. Open your inventory and **use** the laptop.
3. Wait for the **boot sequence** (TOR routing animation).
4. If first time: click **Register** and create an anonymous alias + password.
5. On future uses: click **Login** with your alias and password.

### Buying

1. Browse the **Market** tab, filter by category, or use the search bar.
2. Click a listing card to open the detail modal.
3. Click **Purchase** — funds are held in escrow automatically.
4. After the seller delivers the item at the dead drop coordinates, go to **Orders > Buying** and click **Confirm** to release payment and receive the item.

### Selling

1. Go to the **Sell** tab.
2. Enter the exact item name (must match your inventory).
3. Set quantity, price, condition, and optional description.
4. Enter the **Dead Drop coordinates** (X/Y/Z) where you will leave the item.
5. Click **Preview** to check your listing.
6. Click **Post Listing** — the item is removed from your inventory.
7. When a buyer confirms delivery, you receive payment (minus tax).

### Messages

- Click **Msg Seller** on any listing to open a DM.
- Messages auto-delete after 24 hours (configurable).
- New messages show a badge on the nav and a toast notification.

### Wallet

- Stores DarkCoin balance (used for offline seller payouts).
- Use the **Coin Mixer** to launder coins through a fake routing animation.

---

## Laptop Tiers

| Item | Boot Delay | Heat Multiplier | Max Listings | Encrypted DMs |
|------|-----------|----------------|--------------|---------------|
| `darkmet_laptop` | 4.0s | 1.0x | 5 | No |
| `darkmet_laptop_pro` | 2.5s | 0.7x | 10 | Yes |
| `darkmet_laptop_elite` | 1.2s | 0.4x | 20 | Yes |

---

## Reputation System

Reputation is based on completed sales (seller side only):

| Skulls | Label | Minimum Sales |
|--------|-------|---------------|
| o | Unknown | 0 |
| skull | Rookie | 5 |
| skull x2 | Dealer | 20 |
| skull x3 | Trusted | 50 |
| skull x4 | Elite | 100 |
| skull x5 | Legendary | 250 |

---

## Heat System

Heat accumulates as players use the marketplace:

| Action | Heat Gained |
|--------|-------------|
| Browsing a listing | +0.5 |
| Creating a listing | +3.0 |
| Purchasing | +5.0 |

- Heat decays at **2 points/minute** while the UI is open.
- At 80% heat: notification sent to all online police officers.
- Heat multiplier is reduced by using a Pro or Elite laptop.

**Honeypot listings** (random 5% chance):
- Trigger instant heat spike to 100%
- Force-close the connection
- Send immediate police alert

---

## Escrow System

1. Buyer pays -> funds deducted immediately, held server-side.
2. Seller delivers item in-game at the dead drop.
3. Buyer clicks **Confirm** in Orders -> funds released to seller (minus tax).
4. If buyer never confirms: funds auto-release to seller after `Config.EscrowTimeout` hours.
5. If seller is offline at release time: funds go to their wallet balance.

---

## Police Integration

- Police job name is set via `Config.PoliceJob` (default: `'police'`).
- When a player's heat hits the threshold, all online officers receive a notification: `[DARKNET] Suspicious network activity detected.`
- Repeat alerts per player are throttled by `Config.AlertCooldown` seconds.
- Honeypot purchases trigger an immediate, unthrottled alert.

---

## Admin Commands

| Command | Permission | Description |
|---------|-----------|-------------|
| `/givelaptop [id] [tier]` | ace `command.givelaptop` | Give a laptop item to a player |
| `/darkmet` (debug only) | none (local) | Open UI without item (debug mode only) |

To grant the givelaptop ace:

```
add_ace group.admin command.givelaptop allow
```
