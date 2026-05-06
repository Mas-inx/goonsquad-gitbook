# Installation

## Step 1: Add to Server

1. Download / clone the `darkmet` folder into your server's `resources` directory.
2. Add to `server.cfg`:
   ```
   ensure oxmysql
   ensure darkmet
   ```
   Make sure `darkmet` starts **after** your framework and `oxmysql`.

## Step 2: Database Setup

Import the SQL file into your server's database:

```bash
mysql -u YOUR_USER -p YOUR_DB < resources/darkmet/sql/darkmet.sql
```

Or paste the contents of `sql/darkmet.sql` into HeidiSQL / phpMyAdmin / DBeaver.

This creates the following tables:

| Table | Purpose |
|-------|---------|
| `darkmet_users` | Accounts, aliases, reputation, heat |
| `darkmet_wallets` | DarkCoin balances and wallet addresses |
| `darkmet_listings` | Active and historical listings |
| `darkmet_orders` | Purchase orders and escrow state |
| `darkmet_messages` | Encrypted DMs between players |

## Step 3: Set Framework

In `shared/config.lua`:

```lua
Config.Framework = 'qbcore'  -- or 'qbox' or 'esx' or 'auto'
```

## Step 4: Add Inventory Items

### QBCore — `qb-core/shared/items.lua`

```lua
['darkmet_laptop'] = {
    name = 'darkmet_laptop',
    label = 'DarkMet Laptop',
    weight = 2000,
    type = 'item',
    image = 'darkmet_laptop.png',
    unique = true,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'An untraceable laptop for accessing the dark web.'
},
['darkmet_laptop_pro'] = {
    name = 'darkmet_laptop_pro',
    label = 'DarkMet Laptop [PRO]',
    weight = 1800,
    type = 'item',
    image = 'darkmet_laptop_pro.png',
    unique = true,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'Pro-grade anonymous laptop. Lower heat generation.'
},
['darkmet_laptop_elite'] = {
    name = 'darkmet_laptop_elite',
    label = 'DarkMet Laptop [ELITE]',
    weight = 1500,
    type = 'item',
    image = 'darkmet_laptop_elite.png',
    unique = true,
    useable = true,
    shouldClose = true,
    combinable = nil,
    description = 'Military-grade anonymous system. Near-zero trace.'
},
```

### QBox — `ox_inventory/data/items.lua`

```lua
['darkmet_laptop'] = {
    label  = 'DarkMet Laptop',
    weight = 2000,
    stack  = false,
    description = 'An untraceable laptop for accessing the dark web.',
    client = {
        image = "darkmet_laptop.png",
    }
},
['darkmet_laptop_pro'] = {
    label  = 'DarkMet Laptop [PRO]',
    weight = 1800,
    stack  = false,
    description = 'Pro-grade anonymous laptop. Lower heat generation.',
    client = {
        image = "darkmet_laptop_pro.png",
    }
},
['darkmet_laptop_elite'] = {
    label  = 'DarkMet Laptop [ELITE]',
    weight = 1500,
    stack  = false,
    description = 'Military-grade anonymous system. Near-zero trace.',
    client = {
        image = "darkmet_laptop_elite.png",
    }
},
```

### ESX — database insert

```sql
INSERT INTO `items` (`name`, `label`, `weight`, `rare`, `can_remove`) VALUES
('darkmet_laptop',       'DarkMet Laptop',        2000, 0, 1),
('darkmet_laptop_pro',   'DarkMet Laptop [PRO]',  1800, 0, 1),
('darkmet_laptop_elite', 'DarkMet Laptop [ELITE]',1500, 0, 1);
```

## Step 5: Restart Server

Run `refresh` then `start darkmet`, or restart your server entirely.

---

## Framework-Specific Setup

### QBCore

1. Set `Config.Framework = 'qbcore'` in `shared/config.lua`.
2. Add items to `qb-core/shared/items.lua` (see above).
3. Ensure `qb-core` starts before `darkmet` in `server.cfg`.

### QBox

1. Set `Config.Framework = 'qbox'` in `shared/config.lua`.
2. Add items to `ox_inventory/data/items.lua` (see above).
3. Ensure `ox_inventory` starts before `darkmet`.

### ESX

1. Set `Config.Framework = 'esx'` in `shared/config.lua`.
2. Insert items into the `items` table (see above).
3. Ensure `es_extended` starts before `darkmet`.

---

## Troubleshooting

**UI doesn't open:**
- Check browser console (F8 in FiveM) for errors.
- Ensure `ui_page` in `fxmanifest.lua` points to `ui/index.html`.
- Try `Config.Debug = true` and run `/darkmet` to open without an item.

**Items not being removed/given:**
- Confirm item names in `Config.AllowedItems` exactly match your inventory.
- Check that your inventory resource is correctly named in the framework bridge.

**Database errors:**
- Run `sql/darkmet.sql` again; it uses `CREATE TABLE IF NOT EXISTS`.
- Ensure `oxmysql` starts before `darkmet`.

**Police not receiving alerts:**
- Confirm `Config.PoliceJob` matches the job name in your framework's job config.

**`qb-core` export not found:**
- Make sure `qb-core` is listed before `darkmet` in `server.cfg`.
