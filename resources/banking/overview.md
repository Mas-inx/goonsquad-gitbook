# GS Banking

An advanced, server-authoritative banking system for FiveM with personal and shared accounts, cards and ATMs, lending, savings, investments, business banking, taxes, administration tools, and compatibility bridges for existing resources.

**Version:** 1.0.0 | **Price:** $19.99 | [Buy on Tebex](https://store.goonsquadstudios.com/package/7619455)

**Product page:** [goonsquadstudios.com/banking](https://goonsquadstudios.com/banking) — full feature breakdown, FAQ and guides

{% embed url="https://www.youtube.com/watch?v=nSF-Qd6LCX0" %}

---

## Features

### Core Banking
- Personal checking, savings, shared, business, job, gang, and government accounts
- Multiple accounts per character with configurable product and total limits
- Unique persistent account numbers (shown as IBANs in the interface)
- Deposits, withdrawals, contacts, internal transfers, and offline IBAN transfers
- Online transfers using a player's current server ID
- Free transfers between the player's own accounts
- Daily, weekly, biweekly, and monthly standing orders
- Searchable activity, running balances, spending categories, and account statements

### Shared and Business Accounts
- Invite-and-accept shared-account membership
- Per-member permission matrix for view, deposit, withdraw, transfer, invoice, payroll, manage, and close access
- Optional daily spending limits per member
- Account conversion and ownership transfer for eligible internal accounts
- Automatically created job and gang accounts
- Employee salary controls, payroll runs, business analytics, and audit attribution
- Two-way balance synchronization with supported legacy society systems

### Cards and ATMs
- Debit, Gold, and Black card products with configurable fees and credit-score requirements
- Physical card items with holder, account, expiry, and last-four metadata
- Card PINs, freeze/unfreeze, lost-card reporting, expiry, and per-card limits
- ATM withdrawal, optional deposit, balance inquiry, mini statements, PIN changes, and receipts
- Per-machine cash pools, brand-specific surcharges, restocking, and broken states
- Optional stolen-card detection, skimmers, dispatch alerts, and ATM robbery integrations

### Credit, Savings, and Wealth
- Credit score from payment history, utilization, account age, activity, and defaults
- Configurable personal, vehicle, property, and business loan products
- Automatic or manual loan approval, amortization schedules, late fees, defaults, and early repayment
- Balance-tiered savings APY with snapshot-based interest accrual
- Savings goals, payday auto-deposits, round-up saving, and fixed-term deposits
- Server-driven stock, crypto, and bond market with holdings, limit orders, stop orders, dividends, and news events

### Payments and Government
- Player and society invoices with due dates, status tracking, tax, decline/cancel actions, and optional autopay
- Payment requests that the recipient can approve or decline
- Progressive income tax plus configurable transfer, withdrawal, sales, and wealth taxes
- Job/gang exemptions and tax holidays
- Tax revenue routed to a government account
- Cheque, dirty-money, police freeze/seizure, transaction trace, and offshore-account exports

### Security and Operations
- Double-entry, append-only ledger with automatic reconciliation
- Server-authoritative validation; clients submit intent, never balances
- Idempotency protection against duplicate UI submissions
- Atomic balance updates, per-endpoint rate limits, PIN lockouts, and AML flagging
- Admin economy dashboard, account search, adjustments, freezes, reversals, and audit log
- Live validated configuration editor and live appearance editor
- Discord webhooks plus optional FiveMerr, Datadog, and generic HTTP logging
- Automatic versioned database migrations and competitor migration commands

---

## Framework Support

| Framework | Support | Account behavior |
|---|---|---|
| Qbox (`qbx_core`) | Full | The default checking account mirrors framework bank money. |
| QBCore (`qb-core`) | Full | The default checking account mirrors framework bank money. |
| ESX Legacy (`es_extended`) | Full | The default checking account mirrors the ESX bank account. |
| ox_core | Basic | Identity and cash are bridged; banking accounts remain internal. |
| Standalone | Basic | Uses the FiveM license identity and internal accounts. |

On Qbox, QBCore, and ESX, existing paychecks, shops, HUDs, and scripts continue to see the framework's normal bank balance. Extra accounts remain owned by GS Banking and are stored in its ledger.

---

## Requirements

| Dependency | Required | Purpose |
|---|---|---|
| oxmysql | Yes | Database access and automatic schema migrations |
| MySQL 8+ or MariaDB 10.4+ | Yes | Persistent accounts, ledger, cards, loans, and settings |
| Qbox, QBCore, or ESX | Recommended | Character identity, cash, and mirrored bank balance |
| Inventory resource | Optional | Physical cards, receipts, cheques, skimmers, and item-based cash |
| Target or TextUI resource | Optional | Bank and ATM interactions; native fallback is included |

See [Installation](installation.md) for the complete setup.

---

## Supported Integrations

| Category | Auto-detected resources |
|---|---|
| Inventory | ox_inventory, qb-inventory, qs-inventory, ps-inventory, codem-inventory, origen_inventory, framework fallback |
| Target | ox_target, qb-target, qtarget, native TextUI/marker fallback |
| Notifications | ox_lib, okokNotify, mythic_notify, ps-ui, framework/NUI fallback |
| Text UI | ox_lib, QBCore DrawText, esx_textui, cd_drawtextui, native fallback |
| Society | qb-management, esx_addonaccount |
| Phone push | lb-phone, qs-smartphone, gksphone, NPWD, RoadPhone, YFlip Phone |
| Dispatch | ps-dispatch, cd_dispatch, qs-dispatch, rcore_dispatch, linden_outlawalert, core_dispatch |

Run `/gsbanking bridge` as an administrator to print every adapter detected on the current server.

---

## Quick Start

1. Follow the [Installation](installation.md) guide.
2. Review the [Configuration](configuration.md), especially the PIN pepper, account products, fees, and feature switches.
3. Start the resource and run `/gsbanking bridge`.
4. Use `/bank` or visit a configured branch.
5. Read the [Usage](usage.md) guide for player workflows and [Administration](administration.md) for staff tools.
