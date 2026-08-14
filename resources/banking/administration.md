# Administration

GS Banking includes an in-game administration area and server commands for economy operations, migrations, account investigation, and maintenance.

---

## Access

A player is considered a banking administrator when either:

- Their framework marks them as an administrator, or
- `IsPlayerAceAllowed` passes one of the entries in `Config.Security.adminAces`

Recommended ACE:

```cfg
add_ace group.admin gsbanking.admin allow
```

{% hint style="danger" %}
Banking administrators can create and remove money. Do not grant `gsbanking.admin` to ordinary moderators or broad public groups.
{% endhint %}

---

## In-Game Administration

Authorized users can open **Administration** inside the bank.

Available tools include:

- Search accounts by holder, name, or IBAN
- Inspect balances, types, owners, and frozen state
- View ledger activity
- Add or remove money with a mandatory reason
- Freeze or unfreeze accounts
- Reverse eligible transactions by posting a new reversing entry
- Review the banking admin audit log
- View circulation, faucets/sinks, top accounts, and inequality statistics
- Edit live configuration
- Edit and broadcast bank appearance

Every admin adjustment, freeze, reversal, theme change, and live-config change is stored in the audit log and sent to the configured admin webhook.

Reversals never edit or delete original history. They post an opposite ledger transaction and mark the relationship for audit purposes.

---

## Live Configuration

The Config panel renders from the resource's server-side schema. Values are validated before they can be applied.

Changes can include:

- Fees, taxes, overdraft, and account limits
- Feature switches and security thresholds
- Account/card pricing
- Branch, loan, APY, term-deposit, tax, investment, news, and ATM-brand rows
- Performance and scheduled-report settings

Branch rows include a **use my location** action. When branch or world-facing configuration is saved, connected clients rebuild affected blips, peds, and interaction zones.

Database overrides take precedence over shipped Lua defaults. Use the panel's restore actions when you want a field or section to follow the file again.

---

## Commands

The default admin command is `gsbank`. It can be renamed in `Config.Commands.admin`.

| Command | Description |
|---|---|
| `/gsbank balance <iban-or-account-id>` | Show account name, current balance, and frozen state |
| `/gsbank add <iban> <amount> <reason>` | Credit an account and write a ledger/admin entry |
| `/gsbank remove <iban> <amount> <reason>` | Debit an account and write a ledger/admin entry |
| `/gsbank freeze <iban> [reason]` | Freeze an account |
| `/gsbank unfreeze <iban> [reason]` | Unfreeze an account |
| `/gsbank inspect <iban>` | Print the ten most recent ledger rows |
| `/gsbank reconcile` | Report balance/ledger drift |
| `/gsbank reconcile fix` | Set drifted internal balances to ledger truth |
| `/gsbank restock` | Fill every tracked ATM to capacity |
| `/gsbank gini` | Print the economy's Gini coefficient |
| `/gsbank export` | Write the economy export file and print its path |
| `/gsbank migrate <source>` | Dry-run a competitor migration |
| `/gsbank migrate <source> apply` | Apply a competitor migration |
| `/gsbank loan <id> approve` | Approve a pending loan in manual mode |
| `/gsbank loan <id> reject` | Reject a pending loan in manual mode |
| `/gsbanking bridge` | Print all detected adapters |

Console use is allowed. In-game use requires banking administrator access.

`Config.Migration.allowRuntime = false` makes migrations console-only.

---

## Migration Workflow

1. Back up the database.
2. Keep the old banking resource stopped unless its tables are needed read-only.
3. Start GS Banking and confirm the schema is ready.
4. Run the appropriate dry-run:

```text
gsbank migrate qb-banking
gsbank migrate renewed
gsbank migrate okok
gsbank migrate esx
gsbank migrate ox
```

5. Confirm table detection and account counts.
6. Apply once with the final `apply` argument.
7. Check several player and society accounts before opening the server.

Framework checking balances are not duplicated. On Qbox, QBCore, and ESX, the default GS account continues to mirror the existing framework bank value.

---

## Economy Reports and Logging

Configure category-specific Discord webhooks in `Config.Webhooks`.

| Category | Typical entries |
|---|---|
| `transactions` | Deposits, withdrawals, and transfers |
| `accounts` | Open, close, freeze, and member changes |
| `cards` | Card issue and state changes |
| `loans` | Applications, funding, repayments, and defaults |
| `invoices` | Invoice issue and settlement |
| `society` | Payroll and society movements |
| `admin` | Adjustments, reversals, and configuration changes |
| `security` | Lockouts, AML flags, police actions, and seizures |
| `economy` | Scheduled economy summary |
| `reconcile` | Ledger drift warnings |

Set `Config.EconomyReportAt = 'HH:MM'` to send a daily economy report using server time.

Optional external sinks are available under `Config.LogServices` for FiveMerr, Datadog, and a generic JSON HTTP endpoint. Keep service keys file-side and private.

---

## Ledger and Reconciliation

GS Banking uses double-entry bookkeeping. Each movement contains balanced debit and credit legs, and the ledger is append-only.

The daily reconciliation job compares ledger truth with stored internal account balances. A drift warning usually means another resource or manual SQL changed `gs_accounts.balance` directly.

If drift appears:

1. Run `/gsbank reconcile` without `fix`.
2. Identify and stop the direct database writer.
3. Back up the database.
4. Use `/gsbank reconcile fix` only after confirming the ledger is the intended truth.

Use `AddMoney`, `RemoveMoney`, `TransferMoney`, or another public export for integrations. Never write balances directly.

---

## Scheduled Processing

One server scheduler handles:

- Standing orders every minute
- Loan and invoice collection every five minutes
- Term-deposit maturity checks every ten minutes
- ATM restocking every fifteen minutes
- Society reconciliation every five minutes
- Configured investment price ticks and hourly news
- Daily interest, balance snapshots, credit snapshots, card expiry, archival, and reconciliation
- Weekly maintenance fees, wealth tax, and dividends

Daily jobs use persistent latches so a resource restart does not run the same settlement twice.

---

## Demo and Development Data

`Config.DemoMode = true` is safe for UI previews because it disables persistent mutations and the scheduler.

Some development copies of the resource may include `server/dev_seed.lua` and the `/gsseed` command. That command creates or clears large amounts of demonstration banking data.

{% hint style="warning" %}
Before a production release, follow the comment in `fxmanifest.lua` and remove the development seed entry/file if it is still present. Never expose seed tooling to ordinary staff.
{% endhint %}

