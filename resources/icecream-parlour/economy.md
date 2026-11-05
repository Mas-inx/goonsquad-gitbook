# Economy & Storage

## Payments and society accounts

Order prices are computed on the server from the menu. The default service percentage is zero. Each shop's job owns its society account; by default 100% of customer order income is credited there. Customer/supply purchases check cash/bank balances before taking money. Staff supplies are paid by the ordering player, not automatically from the society account.

Society tables are gs_icecream_society and gs_icecream_society_transactions. New accounts start at zero. Legacy import is false. Renaming a job starts a new account under the new job name; it does not migrate the old balance.

Withdrawals use a conditional balance update that prevents ordinary overdrafts. Refunds may take the original order's society share back even if that requires a negative society balance. Server exports are for trusted integrations, not direct client use.

## Persistent resource state

Open customer orders, counters and supply orders use server resource KVP. Failed immediate supply-packing/deposit rollback credits use gs_icecream_pending_refunds in the same storage. They are retried every five seconds for the original online character and remain queued while that character is offline or the framework refuses the credit.

Customer/supply ordering waits until saved orders are restored. Society-backed customer orders wait until account initialization completes. Tray transfers are blocked while that order is under server settlement.

Back up the server's resource KVP storage and both society tables. No saved KVP state is included in the release folder/ZIP. Normal restart restoration is supported; a server crash midway through external inventory/money operations still needs reconciliation across those systems.

## Refund and expiry rules

Staff deletion refuses to discard an order when its required customer refund cannot complete. Expired NEW orders similarly wait for the original owner to be online when a refund is required. READY orders can expire according to the configured collection timer. Uncollected supply expiry is disabled by default (zero); enabling it removes orders after that duration.

The trusted DeleteOrder server export performs external removal and does not promise the staff refund flow. Use it deliberately; see [API & Exports](exports.md).

Products, quality metadata, usable-item definitions and ordinary inventory storage remain owned by your inventory/framework. Preserve those definitions and data separately when changing providers.
