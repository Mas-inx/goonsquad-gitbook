# Troubleshooting

Enable `Config.Debug = true` while reproducing a problem, then capture the complete `[gs-banking]` lines from the server console and relevant F8 console output.

Run this first as an administrator:

```text
/gsbanking bridge
```

It reports the framework, inventory, target, notification, TextUI, phone, dispatch, and society adapters currently selected.

---

## Startup and Database

### `oxmysql is not started`

Start oxmysql before GS Banking:

```cfg
ensure oxmysql
ensure gs-banking
```

Confirm oxmysql connected to the intended database before the banking resource starts.

### Schema does not install

- Confirm the database user can create and alter tables.
- Confirm the server uses MySQL 8+ or MariaDB 10.4+.
- Check the first database error above the banking startup failure.
- Do not manually import a partial schema; fix the connection/permission issue and let versioned migrations run.

### `CONFIG ERROR` on startup

GS Banking intentionally refuses to finish booting with invalid settings. Read every printed validation error.

Common causes:

- Credit weights do not total 100.
- Income tax brackets overlap or contain gaps.
- A range has a minimum greater than its maximum.
- A required final APY/tax row is missing.
- A live database override is invalid after a file change.

Fix the reported field or restore its live override, then restart.

### Tables exist but nothing persists

Confirm `Config.DemoMode = false`. Demo mode performs no banking database writes and disables scheduled jobs.

---

## Framework and Balance Problems

### Wrong framework detected

1. Make sure the framework starts before GS Banking.
2. Run `/gsbanking bridge`.
3. If necessary, force the correct adapter:

```lua
Config.Framework = 'qbx' -- or 'qb', 'esx', 'ox'
```

Restart after changing a framework bridge.

### Existing Qbox/QBCore/ESX bank balance is missing

The default checking account should mirror the framework bank value, not copy it into `gs_accounts.balance`.

- Confirm the character identifier is correct.
- Confirm `GetBank` works in the selected framework bridge.
- Confirm the default account has `kind = 'framework'`.
- Do not import the same framework bank amount as a second internal balance.
- Restart GS Banking after the framework and inspect startup adapter output.

### Balance changes in another resource do not appear

- Confirm that resource changes the framework bank through normal framework APIs.
- Confirm GS Banking started early enough to subscribe to framework money events.
- Run `/gsbanking bridge` and verify the framework.
- Reconnect or restart GS Banking once to establish a clean balance baseline.

### Society balance differs from qb-management or esx_addonaccount

Society reconciliation runs every five minutes.

- Verify the society provider started before GS Banking.
- Verify `/gsbanking bridge` shows `qbmanagement` or `esxsociety`.
- Check the society name matches on both sides.
- Do not run two competing society providers for the same account.
- Wait for one reconciliation cycle or restart after fixing startup order.

---

## Bank and ATM Interactions

### `/bank` does nothing

- Confirm `Config.OpenCommand = 'bank'` and the resource fully started.
- Check F8 for NUI or callback errors.
- Confirm the player framework object is available.
- Confirm the folder is named `gs-banking`.
- Test at a configured branch to separate command and UI problems.

### Banker ped or target does not appear

- Confirm the target resource started first.
- Check `/gsbanking bridge` for the target and TextUI adapters.
- Verify branch coordinates in `Config.Banks`.
- Confirm `Config.BankPed.enabled = true` if a ped is expected.
- If no target is installed, approach within `Config.InteractDist` and test the native fallback.

### ATM interaction does not appear

- Confirm `Config.Features.atm = true`.
- Confirm the prop model is listed in `Config.AtmModels`.
- Confirm the target adapter started first.
- Stand close to the actual prop; the server rejects sessions outside `Config.AtmMaxDistance`.
- Add custom ATM props by model name and restart/rebuild interactions.

### ATM says it is empty, broken, or cannot deposit

- Cash pools deplete as players withdraw.
- Use `/gsbank restock` for an administrative refill.
- A robbery can keep a machine broken for the configured period.
- Deposit availability is per ATM brand and also requires `Config.Features.atmDeposit`.
- Confirm the model is mapped to the intended brand.

---

## Cards and Inventory

### Card was charged but no item appeared

- Install `gs_bankcard` in the active inventory.
- Confirm the inventory resource starts before GS Banking.
- Run `/gsbanking bridge` and verify the inventory adapter.
- Confirm `Config.Cards.item = 'gs_bankcard'` and `giveItem = true`.
- Check inventory capacity and item-definition errors.

The banking card record may still exist even when inventory delivery fails. Resolve the item setup before ordering replacement cards.

### Physical card metadata is missing

ox_inventory and compatible QB inventories preserve metadata. Some framework-native inventory fallbacks only support item counts and cannot enumerate card metadata.

Use a supported metadata inventory when `Config.RequireCardAtAtm = true` or stolen-card behavior is important.

### Card remains pending

A card without a valid PIN remains pending. Open Cards or use the ATM PIN flow to set a four-digit card PIN.

### Card is frozen after PIN attempts

The default policy freezes a card after three failed attempts. The holder can review its state in Cards. Staff should investigate stolen-card/security events before restoring access.

### Receipt, cheque, or skimmer item is missing

Install the matching definition and image:

- `gs_receipt`
- `gs_cheque`
- `gs_skimmer`

Confirm the configured item name matches the inventory definition exactly.

---

## PIN and Security

### Existing PINs stopped working after configuration changes

Restore the original `Config.Security.pepper`. PIN hashes cannot be verified with a different pepper.

If the original is permanently lost, a deliberate PIN reset/migration is required. Back up the database and plan the reset rather than repeatedly changing the pepper.

### Large transfer asks for a PIN unexpectedly

Check:

```lua
Config.Security.pinRequired.transferAbove
```

The extra prompt applies only when the player has set a banking PIN and the transfer meets the threshold. This is the banking PIN, not the selected card's ATM PIN.

### Player is rate-limited

Repeated or duplicate UI requests can exhaust the endpoint token bucket.

- Wait briefly and try once.
- Check for an NUI integration repeatedly firing the same callback.
- Check F8 for loops or duplicate click handlers.
- Do not weaken rate limits until the caller is fixed.

---

## Transfers, Invoices, and Scheduled Payments

### IBAN is not found

- Copy the complete IBAN from the destination account.
- Spaces and case are accepted, but missing or changed characters are not.
- Confirm the destination account is not archived.
- Confirm both players use the same GS Banking database.

### Player ID transfer cannot find the recipient

Player ID transfers work only while the recipient is online.

- Confirm the current server ID, not citizen ID or license.
- Confirm the recipient finished loading into the framework.
- Use the recipient's IBAN for offline transfers.

### Player ID cannot be scheduled

This is intentional. Server IDs are temporary and can belong to a different player later. Create the standing order with a saved contact or IBAN.

### Transfer total is higher than the amount

External transfers may include a transfer fee and government levy. The summary displays each charge before confirmation.

Review:

```lua
Config.Fees.transfer
Config.Fees.levyPercent
Config.Tax.types.transfer
```

### Invoice recipient is not found

Invoice and payment-request creation expects the framework citizen/character identifier, not a temporary server ID. Confirm that identifier exists in the framework database.

### Autopay did not run while the player was offline

Invoice autopay currently settles through a live player session. The recipient must be online when the due sweep attempts payment. The invoice becomes overdue when it remains unpaid.

Standing orders and configured loan offline repayments use their own settlement paths and can operate differently.

---

## Loans, Savings, and Investments

### Loan application is rejected

Check:

- Amount is within the product range.
- The selected term belongs to the product.
- Credit score meets the minimum.
- Required collateral data is present.
- The player has fewer than `maxActivePerPlayer` active loans.
- The destination account is eligible and not frozen.

In manual mode, an administrator/banker must approve the pending loan.

### Interest did not pay immediately

Interest is scheduled at `Config.Savings.payoutHour` and uses balance snapshots. New accounts need eligible snapshot history before normal accrual is visible.

### Term deposit cannot be opened

- Confirm the amount meets `minAmount`.
- Confirm the player has not reached `maxActive`.
- Confirm the source account has available funds.
- Confirm term deposits are enabled.

### Investment prices do not move

- Confirm investments are enabled.
- Confirm `Config.Investments.tickMinutes` is valid.
- Confirm `Config.DemoMode = false` so the scheduler runs.
- Check the market tables and server console for tick errors.

---

## Administration and Configuration

### Administration tab is missing

- Grant `gsbanking.admin` ACE or use a supported framework admin group.
- Reconnect after changing principals/ACE permissions.
- Confirm `Config.Security.adminAces` contains the intended object.
- Confirm the framework administrator API is working.

### Editing the Lua file has no effect

A live config override may be stored in `gs_meta` and applied over the file default.

Open **Administration → Config** and restore the field/section to defaults, or intentionally update the live value there.

### Admin adjustment requires a reason

This is intentional. Money creation/removal and freezes require an audit reason.

---

## Ledger Drift

### Reconciliation webhook reports drift

Run:

```text
/gsbank reconcile
```

Drift means the internal account balance no longer matches the append-only ledger, usually because another resource or manual SQL wrote `gs_accounts.balance` directly.

1. Identify and stop the direct writer.
2. Back up the database.
3. Inspect affected accounts and ledger history.
4. If the ledger is correct, run `/gsbank reconcile fix`.

Do not treat repeated reconcile fixes as normal maintenance. Integrate external resources through the public exports.

---

## Migration Problems

### Source table not found

Use the source alias expected by GS Banking and confirm the old resource tables are in the same database:

```text
qb-banking
renewed
okok
esx
ox
```

### Accounts were imported twice

Personal migration imports create GS accounts. Applying the migration repeatedly can therefore create additional imported accounts.

Restore the pre-migration database backup or carefully remove the duplicate import with the old resource stopped. Always dry-run and apply once.

---

## Information to Send Support

- GS Banking version
- Framework and framework version
- Output of `/gsbanking bridge`
- Database server type/version
- Whether the affected account is framework-mirrored or internal
- Complete relevant server console block
- Complete relevant player F8 block
- Exact action and amount that reproduced the problem
- Whether live Admin → Config overrides are in use
- Any recently migrated banking resource

Remove database credentials, webhook URLs, API keys, PIN peppers, player licenses, and other private identifiers before sharing logs publicly.

