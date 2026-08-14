# Usage

---

## Opening the Bank

Players can open GS Banking in any enabled way:

- Run `/bank`
- Use a configured banker ped or branch interaction
- Use a configured keybind
- Open it from another resource through the `OpenBank` client export

The default branch locations are configured in `config/locations.lua`.

ATMs open by interacting with a configured ATM prop. They use a separate, card-focused interface and may have different deposits, surcharges, cash availability, or broken status depending on the machine's brand.

---

## Home and Activity

The Home page provides:

- Current and available balance
- Cash carried by the player
- Credit score
- Recent income and spending
- Cash-flow and category charts
- Recent transactions
- Upcoming invoices and payments
- Quick transfer, deposit, and withdrawal actions

The Activity page contains deeper transaction history, filters, statement details, counterparty information, and running balances.

---

## Accounts

### Opening an Account

1. Open **Accounts**.
2. Select **Open account**.
3. Choose an available product.
4. Enter a name and opening deposit.
5. Choose cash or an existing accessible account as the funding source.
6. Confirm the opening fee and deposit.

The server checks the product limit, total account cap, minimum deposit, funding permissions, and available balance before creating the account.

### Account Types

| Type | Typical use |
|---|---|
| Checking | Everyday payments; the default account mirrors framework bank money on Qbox/QBCore/ESX |
| Savings | Interest-bearing internal account |
| Shared | Account with invited members and individual permissions |
| Business | Operating account with business tools |
| Job/Gang | Society account created for an eligible organization |

### Account Management

Eligible owners and managers can:

- Rename an account
- Copy its IBAN
- Make it the primary account
- Freeze or unfreeze it
- View limits and recent activity
- Manage shared members and daily allowances
- Convert eligible internal checking/shared accounts
- Transfer ownership of eligible non-primary internal accounts
- Close an account and sweep its remaining balance

An owner cannot unfreeze an account that staff or law enforcement froze.

---

## Deposits and Withdrawals

Open **Payments → Move money**, choose **Deposit** or **Withdraw**, select the account, and enter an amount.

- Deposits remove carried cash and credit the selected account.
- Withdrawals debit the account and give carried cash.
- Item-based cash is used when `Config.Cash.asItem = true`.
- Fees, tax, account limits, holds, and overdraft availability are checked by the server.

ATM withdrawals may also be restricted by the selected card's daily ATM limit and the machine's remaining cash pool.

---

## Transfers

Open **Payments → Move money → Send**. Choose one of four recipient methods.

### Saved Contact

Select a previously saved recipient. Contacts store the recipient account's IBAN, so the recipient can be offline.

### IBAN

Every GS Banking account has a unique persistent account number called an IBAN in the interface.

1. Ask the recipient to copy the IBAN from their account page.
2. Choose **IBAN**.
3. Paste or enter the complete number.
4. Enter the amount and optional reference.
5. Review the fee, levy, total, and remaining balance.
6. Confirm the transfer.

Spaces and capitalization are normalized by the server. The destination account is looked up in the GS Banking database, so an IBAN transfer works while the owner is offline.

{% hint style="info" %}
The in-game IBAN is a permanent GS Banking account identifier. It is not a real-world international bank account and does not connect outside the FiveM server.
{% endhint %}

### Player ID

Choose **Player ID** and enter the recipient's current FiveM server ID.

- The recipient must be online.
- The server resolves the ID through Qbox, QBCore, or ESX and credits that character's primary account.
- The browser never supplies or chooses the recipient's citizen ID.
- After a successful transfer, the resolved account is saved as a normal IBAN contact.

Player IDs are temporary and may be reused after disconnects. Confirm the ID immediately before sending.

### My Accounts

Choose **Mine** to move money between your own accessible accounts. Own-account transfers are instant and fee-free.

### Transfer Charges and Limits

External transfers can include:

- The configured transfer fee
- A government transfer levy when transfer tax is enabled
- Per-transaction, daily transfer, and shared-member limits
- PIN confirmation above the configured threshold

The recipient receives the entered transfer amount. Fees and levies are additional debits shown in the summary.

---

## Scheduled Payments

IBANs, saved contacts, and the player's own accounts can be used for scheduled payments.

1. Prepare a transfer.
2. Enable **Repeat this payment**, or open the **Scheduled** page to create a new order.
3. Choose one of the available daily, weekly, biweekly, or monthly schedules.
4. Confirm the standing order.

Standing orders store the destination IBAN and can run while the recipient is offline. They re-check permissions, limits, balance, fees, and tax each time they run.

Player IDs cannot be scheduled because a server ID does not permanently identify a character.

The **Scheduled** page lets players pause, resume, or delete their orders and review the next run.

---

## Contacts

Contacts make repeated IBAN transfers easier.

- A successful manual IBAN or Player ID transfer can save the resolved recipient.
- Players can add a known IBAN manually.
- Contacts can be renamed, favorited, searched, and removed.
- The saved entry uses the account IBAN, not a temporary server ID.

---

## Invoices and Payment Requests

### Invoices

Invoices support player and authorized society billing.

The issuer supplies:

- Recipient citizen/character identifier
- Amount
- Reason
- Due period
- Optional society attribution and autopay

The recipient can pay from an accessible account or decline an unpaid/overdue invoice. The issuer can cancel an unpaid/overdue invoice. Society-issued invoices require invoice permission on the society account.

Overdue invoices can affect credit score. When configured, sales tax is included and routed to the government account.

### Payment Requests

A payment request asks another character to approve a transfer. The recipient can accept or decline it; acceptance transfers from their primary account to the requester's primary account.

---

## Cards

Open **Cards** to order and manage cards linked to accessible accounts.

Depending on configuration, players can:

- Order Debit, Gold, or Black products
- Choose and change a PIN
- Enable or disable contactless use
- Reduce ATM, spending, or contactless limits within the product maximum
- Freeze/unfreeze a card
- Report a card lost
- View today's card and ATM usage

Card issue fees and minimum credit scores are enforced server-side. A card without a PIN remains pending until a PIN is set.

If physical items are enabled, the issued inventory item contains the card ID, holder, citizen ID, IBAN, tier, last four digits, and expiry.

---

## ATMs

At an ATM:

1. Select an eligible card.
2. Enter the card PIN when required.
3. Choose withdrawal, deposit, balance, mini statement, PIN change, or receipt.

ATM behavior depends on configuration:

- Some brands charge a surcharge.
- Some machines do not accept deposits.
- Withdrawals cannot exceed the card limit, account limit, or machine cash pool.
- Robbed machines may remain broken temporarily.
- Too many incorrect PIN attempts can freeze the card and notify its owner.

---

## Savings and Wealth

### Savings Accounts

Savings interest is based on daily balance snapshots. The lowest balance observed for the day is used to prevent last-minute deposits from farming interest.

### Savings Goals

Players can create named goals, choose a target, fund them from the linked account, and optionally use payday auto-deposits when enabled.

### Term Deposits

Players can lock an eligible amount for a configured term and APR. Breaking it early returns the principal minus the configured penalty.

### Credit and Loans

The credit page shows score history and factor breakdown. Players can compare loan products, terms, APR, installment schedule, and total repayment before applying.

Loans may require collateral or manual banker approval. Scheduled collection handles installments, late fees, missed payments, settlement, and default.

### Investments

The investments page provides configured stocks, crypto assets, and bonds. Players can buy or sell at market, place supported limit/stop orders, view holdings and profit/loss, and receive configured dividends.

Market prices update server-side and persist across restarts.

### Tax

The tax page shows configured tax categories and the player's recorded tax history. Income, transfers, withdrawals, sales, and wealth may be taxed independently.

---

## Shared Accounts

Account managers invite members by citizen/character identifier. The invited player must accept before access becomes active.

Permissions can be granted individually:

| Permission | Allows |
|---|---|
| `view` | See account and statements |
| `deposit` | Deposit cash |
| `withdraw` | Withdraw funds |
| `transfer` | Send money and pay invoices |
| `invoice` | Issue society-attributed invoices |
| `payroll` | Run payroll |
| `manage` | Rename and manage members/settings |
| `close` | Close an eligible account |

Managers can also assign a per-member daily spending limit. Ledger entries retain the acting character for audit history.

---

## Business and Society Banking

Bosses can receive job or gang accounts automatically. The Business page provides:

- Operating balance and activity
- Income/expense analytics
- Employee list and salary values
- Per-member limits
- Payroll runs
- Shared-account permissions and audit attribution

When qb-management or esx_addonaccount is detected, balances synchronize in both directions so legacy business resources remain compatible.

---

## Security and Settings

The Security page allows players to:

- Set or change their banking PIN
- Enable configured security preferences
- Review security events
- Freeze their accounts and cards quickly

The Settings page stores theme, display, sound, reduced-motion, and notification preferences where supported.

Banking PINs and card PINs are separate. ATM verification uses the selected card's PIN; app and large-transfer confirmation use the character's banking PIN.
