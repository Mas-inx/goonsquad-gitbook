# Configuration

All configuration is in `shared/config.lua`. Every option is documented inline below.

---

## Framework

```lua
Config.Framework = 'auto'
```

How the script talks to your server: inventory, money, jobs, usable items.

| Value | Description |
|-------|-------------|
| `'auto'` | Pick first match: qbx_core -> qb-core -> es_extended (recommended) |
| `'qbox'` | Qbox + ox_inventory (force if auto mis-detects) |
| `'qbcore'` | QBCore + ox_inventory / qb-inventory style |
| `'esx'` | ESX legacy |

---

## General

```lua
Config.LaptopItem = 'darkmet_laptop'
```
Inventory item name for the base DarkMet laptop (must match your items.lua).

```lua
Config.VPNItem = 'tablet'
```
Item counted as VPN for heat reduction when used from the laptop.

```lua
Config.TaxRate = 0.05
```
Seller fee on completed sales. `0.05` = 5% withheld.

```lua
Config.EscrowEnabled = true
```
`true` = buyer's DRK held until delivery confirmed or timeout. `false` = instant completion.

```lua
Config.EscrowTimeout = 72
```
Hours before pending escrow orders auto-pay the seller.

```lua
Config.MaxListingsPerPlayer = 10
```
Hard cap on active listings per player (also capped by laptop tier).

```lua
Config.MaxMessageAge = 24
```
Hours until DMs expire and are deleted.

```lua
Config.MessageCooldownSeconds = 3
```
Minimum seconds between sends per player for DMs.

---

## Heat System

```lua
Config.HeatGainBrowse = 0.5
```
Heat added when browsing the marketplace grid (per refresh that hits the server).

```lua
Config.HeatGainPurchase = 5
```
Heat added when completing a purchase.

```lua
Config.HeatGainList = 3
```
Heat added when creating a new listing.

```lua
Config.HeatBrowseCooldownSeconds = 4
```
Seconds between browse requests that count for heat.

```lua
Config.HeatDecayEnabled = true
```
Slowly reduce heat over time.

```lua
Config.HeatDecayRate = 2
```
Points removed each decay tick per eligible player.

```lua
Config.HeatDecayIntervalSeconds = 60
```
Seconds between decay ticks.

```lua
Config.HeatDecayOnlyWhenDarkmetOffline = true
```
`true` = only decay heat for players without active session. `false` = decay everyone with heat > 0.

```lua
Config.HeatPoliceAlertEnabled = true
```
Send alerts to police when heat threshold is reached.

```lua
Config.PoliceAlertThreshold = 80
```
Heat value (0-100) that triggers police alert.

```lua
Config.PoliceAlertCooldownSeconds = 120
```
Seconds before same player can trigger another heat alert.

---

## Raid System

```lua
Config.RaidEnabled = false
```
Enable raid mode: hard shutdown for very high heat.

```lua
Config.RaidHeatThreshold = 95
```
Heat at which raid logic fires.

```lua
Config.RaidWarningSeconds = 12
```
Seconds of warning before laptop closes.

```lua
Config.RaidShutdownMarketplace = true
```
Broadcast marketplace refresh / close laptop for everyone when raid fires.

```lua
Config.RaidResetHeatAfter = true
```
Reset suspect's heat after raid handling.

```lua
Config.RaidCooldownSeconds = 300
```
Seconds before same player can be raid-triggered again.

---

## VPN

```lua
Config.VPNRequireItem = true
```
Player must have VPN item in inventory to activate VPN.

```lua
Config.VPNHeatReduction = 25
```
Heat subtracted when VPN is activated (clamped to 0).

```lua
Config.VPNDurationSeconds = 300
```
How long VPN buff lasts (client shows countdown).

```lua
Config.VPNConsumeItem = true
```
`true` = remove one VPN item per use.

---

## Honeypot

```lua
Config.HoneypotChance = 0.05
```
Chance (0-1) that a listing is a honeypot trap.

```lua
Config.MinPasswordLength = 6
```
Minimum password length for registration.

---

## Bounty System

```lua
Config.BountyEnabled = true
```
Master switch for bounty board.

```lua
Config.BountyTaxRate = 0.05
```
Fraction kept from hitman payout (0.05 = 5%).

```lua
Config.BountyMinPayout = 500
```
Minimum DRK that can be placed on a contract.

```lua
Config.BountyMaxActive = 1
```
How many active contracts a hitman can hold at once.

```lua
Config.BountyExpiryHours = 48
```
Hours until open/accepted bounties expire.

```lua
Config.BountyHeatGainKill = 15
```
Heat added to hitman on kill completion.

```lua
Config.BountyHeatGainPost = 5
```
Heat added to poster when posting a bounty.

```lua
Config.BountyRequireRep = 5
```
Minimum account total_sales required to accept contracts.

---

## Notifications & Session

```lua
Config.UseCustomNotifications = true
```
`true` = DarkMet NUI toasts even when laptop closed. `false` = qb/ESX notify when closed, NUI when open.

```lua
Config.AutoLoginSavedCredentials = true
```
Try auto-login from saved alias/password (localStorage).

```lua
Config.NotifyAllLoggedInOnNewBounty = true
```
Broadcast new bounty to all logged-in DarkMet users.

---

## Wallet / Economy

```lua
Config.BlackMoneyAccount = 'black_money'
```
Inventory item name for dirty money.

```lua
Config.WalletDepositRate = 1.0
```
DRK received per $1 of black money deposited.

```lua
Config.WalletWithdrawFee = 0.10
```
Fraction kept when withdrawing DRK to black money.

```lua
Config.WalletTransferFee = 0.02
```
Fraction kept on wallet-to-wallet DRK transfers.

```lua
Config.MixerFee = 0.24
```
Fraction kept when mixing DRK to clean cash.

```lua
Config.CleanMoneyAccount = 'cash'
```
Item/account name for clean payout from mixer.

---

## Item List Mode

```lua
Config.ItemListMode = 'blacklist'
```

| Value | Description |
|-------|-------------|
| `'whitelist'` | Only `Config.AllowedItems` may be listed |
| `'blacklist'` | Any item except `Config.BlacklistItems` may be listed |

```lua
Config.BlacklistItems = {
    'water', 'bread', 'sandwich', 'bandage', 'painkillers',
    'armor', 'phone', 'id_card', 'driver_license',
    'darkmet_laptop', 'darkmet_laptop_pro', 'darkmet_laptop_elite',
}
```
Items never allowed as listings (when using blacklist mode).

---

## Allowed Items (Whitelist Mode)

Only used when `Config.ItemListMode = 'whitelist'`. Each entry defines an item's category and price bounds:

```lua
Config.AllowedItems = {
    { item = 'weapon_pistol',       category = 'weapons',   minPrice = 500,   maxPrice = 5000   },
    { item = 'weapon_combatpistol', category = 'weapons',   minPrice = 800,   maxPrice = 8000   },
    { item = 'weapon_microsmg',     category = 'weapons',   minPrice = 2000,  maxPrice = 15000  },
    { item = 'weapon_smg',          category = 'weapons',   minPrice = 3000,  maxPrice = 20000  },
    { item = 'weapon_assaultrifle', category = 'weapons',   minPrice = 8000,  maxPrice = 50000  },
    { item = 'weapon_sniperrifle',  category = 'weapons',   minPrice = 15000, maxPrice = 80000  },
    { item = 'weapon_rpg',          category = 'weapons',   minPrice = 50000, maxPrice = 200000 },
    { item = 'ammo_pistol',         category = 'weapons',   minPrice = 50,    maxPrice = 500    },
    { item = 'ammo_rifle',          category = 'weapons',   minPrice = 100,   maxPrice = 1000   },
    { item = 'weed_og_kush',        category = 'drugs',     minPrice = 100,   maxPrice = 2000   },
    { item = 'weed_ak47',           category = 'drugs',     minPrice = 120,   maxPrice = 2500   },
    { item = 'coke',                category = 'drugs',     minPrice = 500,   maxPrice = 10000  },
    { item = 'meth',                category = 'drugs',     minPrice = 800,   maxPrice = 15000  },
    { item = 'heroin',              category = 'drugs',     minPrice = 1000,  maxPrice = 20000  },
    { item = 'oxy',                 category = 'drugs',     minPrice = 200,   maxPrice = 5000   },
    { item = 'fake_id',             category = 'documents', minPrice = 500,   maxPrice = 5000   },
    { item = 'fake_driverslicense', category = 'documents', minPrice = 300,   maxPrice = 3000   },
    { item = 'hacked_bank_card',    category = 'documents', minPrice = 1000,  maxPrice = 10000  },
    { item = 'usb_drive',           category = 'hacking',   minPrice = 200,   maxPrice = 3000   },
    { item = 'signal_jammer',       category = 'hacking',   minPrice = 2000,  maxPrice = 15000  },
    { item = 'laptop_virus',        category = 'hacking',   minPrice = 5000,  maxPrice = 30000  },
    { item = 'rolex',               category = 'stolen',    minPrice = 2000,  maxPrice = 20000  },
    { item = 'diamond',             category = 'stolen',    minPrice = 5000,  maxPrice = 50000  },
    { item = 'goldbar',             category = 'stolen',    minPrice = 10000, maxPrice = 80000  },
    { item = 'burner_phone',        category = 'misc',      minPrice = 100,   maxPrice = 2000   },
    { item = 'radio',               category = 'misc',      minPrice = 200,   maxPrice = 3000   },
}
```

---

## Laptop Tiers

```lua
Config.LaptopTiers = {
    ['darkmet_laptop'] = {
        label          = 'Standard Laptop',
        bootDelay      = 4000,
        heatMultiplier = 1.0,
        maxListings    = 5,
    },
    ['darkmet_laptop_pro'] = {
        label          = 'Pro Laptop',
        bootDelay      = 2500,
        heatMultiplier = 0.7,
        maxListings    = 10,
    },
    ['darkmet_laptop_elite'] = {
        label          = 'Elite Laptop',
        bootDelay      = 1200,
        heatMultiplier = 0.4,
        maxListings    = 20,
    },
}
```

| Item | Boot Delay | Heat Multiplier | Max Listings |
|------|-----------|----------------|--------------|
| `darkmet_laptop` | 4.0s | 1.0x | 5 |
| `darkmet_laptop_pro` | 2.5s | 0.7x | 10 |
| `darkmet_laptop_elite` | 1.2s | 0.4x | 20 |

---

## Dead Drop Blip

```lua
Config.DeadDropBlip = {
    sprite  = 161,
    color   = 1,
    scale   = 0.8,
    label   = 'Dead Drop',
    timeout = 300,
}
```

---

## Police / Dispatch

```lua
Config.PoliceJob = 'police'
Config.AlertCooldown = 300
```

---

## Zone Restrictions

Leave `Config.AllowedZones` as `{}` to allow access from anywhere. Restrict to specific areas:

```lua
Config.AllowedZones = {
    { coords = vector3(-1037.5, -2738.4, 20.17), radius = 50.0, label = 'Sandy Shores' },
}
```

---

## Database Table Names

Only change if you renamed the MySQL tables:

```lua
Config.Tables = {
    users               = 'darkmet_accounts',
    listings            = 'darkmet_listings',
    orders              = 'darkmet_transactions',
    messages            = 'darkmet_messages',
    wallets             = 'darkmet_wallets',
    wallet_logs         = 'darkmet_wallet_logs',
    bounties            = 'darkmet_bounties',
    groups              = 'darkmet_groups',
    group_members       = 'darkmet_group_members',
    group_messages      = 'darkmet_group_messages',
    group_member_stints = 'darkmet_group_member_stints',
}
```

---

## Debug

```lua
Config.Debug = false
```
`true` = extra server prints where `DebugLog()` is used.
