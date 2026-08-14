# Installation

## Step 1: Requirements

GS Banking requires:

- **oxmysql**
- **MySQL 8+ or MariaDB 10.4+**
- A supported framework, or the built-in standalone identity mode

Qbox, QBCore, and ESX receive full framework-bank mirroring. ox_core and standalone installations use internal GS Banking balances.

{% hint style="warning" %}
Keep the folder name exactly `gs-banking`. Other resources call its exports using that name.
{% endhint %}

---

## Step 2: Add to Server

1. Copy the `gs-banking` folder into your server's resources directory.
2. Start the framework, inventory, target, and other optional integrations before GS Banking.
3. Add the resource after `oxmysql` in `server.cfg`.

Example Qbox order:

```cfg
ensure oxmysql
ensure ox_lib
ensure qbx_core
ensure ox_inventory
ensure ox_target
ensure gs-banking
```

Example QBCore order:

```cfg
ensure oxmysql
ensure qb-core
ensure qb-inventory
ensure qb-target
ensure gs-banking
```

Example ESX order:

```cfg
ensure oxmysql
ensure es_extended
ensure esx_addonaccount
ensure gs-banking
```

Only `oxmysql` is a hard resource dependency. Remove optional lines that your server does not use.

---

## Step 3: Secure the PIN Pepper

Open `config/config.lua` and replace the shipped PIN pepper with a long, server-specific secret before players create PINs:

```lua
Config.Security = {
    pepper = 'replace-this-with-a-long-random-server-secret',
    -- other settings...
}
```

The pepper is mixed into every banking and card PIN hash.

{% hint style="danger" %}
Set the pepper once and keep it private. Changing it after players create PINs invalidates those PINs. Do not publish it in screenshots, logs, or public repositories.
{% endhint %}

If the shipped placeholder is left in place on a new installation, GS Banking generates and stores a random pepper. Supplying your own value before first use is still recommended because it makes backup and recovery behavior explicit.

---

## Step 4: Admin Permissions

GS Banking accepts framework administrators and any ACE listed in `Config.Security.adminAces`.

Recommended ACE setup:

```cfg
add_ace group.admin gsbanking.admin allow
```

To grant a specific principal:

```cfg
add_ace identifier.license:YOUR_LICENSE gsbanking.admin allow
```

Administrators can create or remove money, reverse transactions, freeze accounts, and change live configuration. Grant this permission only to trusted staff.

---

## Step 5: Install Inventory Items (Optional)

The resource includes ready-made ox_inventory definitions and images under:

```text
install/ox_inventory/
```

For ox_inventory:

1. Copy the four PNG files from `install/ox_inventory/images/` into `ox_inventory/web/images/`.
2. Paste the entries from `install/ox_inventory/items.lua` into `ox_inventory/data/items.lua`.
3. Restart ox_inventory or the server.

| Item | Used for |
|---|---|
| `gs_bankcard` | Physical bank cards and card metadata |
| `gs_receipt` | Printed ATM receipts |
| `gs_cheque` | Cheques created through the server export |
| `gs_skimmer` | Optional ATM skimmer system |

For qb-inventory and compatible inventories, use the same item names and copy the images into that inventory's image folder.

If you do not want physical card items:

```lua
Config.Cards.giveItem = false
Config.RequireCardAtAtm = false
Config.AtmReceiptItem = false
Config.Crime.skimmers.enabled = false
```

Cards still exist in the banking interface when `giveItem` is disabled.

---

## Step 6: Review Configuration

At minimum, review:

```lua
Config.Framework = 'auto'
Config.Inventory = 'auto'
Config.Target = 'auto'
Config.Locale = 'en'

Config.OpenCommand = 'bank'
Config.RequireCardAtAtm = false
Config.Cash.asItem = false
```

Then review:

- Account products, fees, and limits in `config/accounts.lua`
- Card products in `config/cards.lua`
- Loan and credit rules in `config/loans.lua`
- Taxes in `config/tax.lua`
- Branches and ATM brands in `config/locations.lua`
- Criminal and police integrations in `config/crime.lua`

See [Configuration](configuration.md) for the full guide.

---

## Step 7: First Start

No SQL import is required. On first start, GS Banking automatically creates and migrates its database tables.

Watch the server console for:

- `schema v... ready`
- The detected framework and integration adapters
- `CONFIG ERROR` messages
- Missing oxmysql or unreachable framework errors

The resource refuses to finish starting when its configuration is invalid.

After startup:

1. Join the server.
2. Run `/gsbanking bridge` as an administrator.
3. Run `/bank` or visit a configured branch.
4. Confirm a default checking account was created.
5. Test a deposit, withdrawal, and transfer with small amounts.
6. Test an ATM and physical card if those features are enabled.

---

## Migrating from Another Banking Resource

Supported migration sources:

- qb-banking
- Renewed-Banking
- okokBanking
- esx_addonaccount
- ox_banking

Always run a dry-run first:

```text
gsbank migrate qb-banking
gsbank migrate renewed
gsbank migrate okok
gsbank migrate esx
gsbank migrate ox
```

Apply only after checking the reported counts:

```text
gsbank migrate qb-banking apply
```

Imported internal balances become opening balances so the GS ledger starts clean. Framework bank money in Qbox, QBCore, and ESX remains in the framework database and is mirrored automatically.

{% hint style="warning" %}
Back up the database before applying a migration. Do not repeatedly run an applied personal-account migration unless you intentionally want additional imported accounts.
{% endhint %}

---

## Compatibility Shims

The following shims are enabled by default in `Config.Compat`:

- Common `qb-banking` exports
- Renewed-Banking account exports
- `esx_addonaccount:getSharedAccount`

This lets many existing job, gang, and business resources continue working without edits. When qb-management or esx_addonaccount is running, society balances are synchronized in both directions and reconciled every five minutes.

See [API & Exports](exports.md) for the supported compatibility surface.

---

## Demo Mode

For UI previews on a development server:

```lua
Config.DemoMode = true
```

Demo mode uses bundled interface data, performs no database writes, and disables scheduled processing. Never use it on a live economy.

