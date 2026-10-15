# Book / account data guide (FX MCP)

Use these tools when the user means **their** (or their client's) platform book. Do **not**
invent cash flows, notionals, or hedge ratios.

## Tools

| Tool | Level | Purpose |
|---|---|---|
| `my_entities` | CLIENT_VIEW+ | Pick a company / entity |
| `my_profile` | CLIENT_VIEW+ | Org/company fields (`whoami` is principal-only) |
| `my_cash_flows` | PROVIDER+ | Page exposures; optional `entity_id` |
| `my_trades` | PROVIDER+ | Page trades; match in compose (no CF filter yet) |
| `my_risk_profile` | PROVIDER+ | `(entity_id, currency)` policy document |
| `excel_hedge_from_book` | PROVIDER+ | Ladder from book → live pricing + simulation |
| `hedge_effectiveness` | PROVIDER+ | Trades vs gross CF notional (remaining reported) |
| `strategy_prior` | PROVIDER+ | Table + one-line rationale from the risk profile |

Condition a TARF versus a forward on `strategy_prior`. Do not invent a second policy.

`CLIENT_VIEW` sees entities/profile (and the usual CLIENT_VIEW market tools) but not
book money tools; platform 403s cash-flow / trade / compose reads.

## Paging

Every list returns `Page[T]`: `items`, `count`, `page`, `page_size`, `has_more`.
**Page until `has_more` is false.** Cap `page_size` at 100. Sequential paging is the
accepted snapshot; there is no single snapshot endpoint.

## Remaining unhedged

One cash flow = one remaining foreign notional at maturity.

  remaining_unhedged = notional * (1 - current_hedge_ratio)

`notional` is **foreign**. Hedge ratio can lag. Skip `is_nav` rows when building a
monthly TARF ladder. Derive `client_side` from `is_base_sold` (true → `SELLS_BASE`).

`hedge_effectiveness` coverage uses **gross** `notional` as the exposure base (trades /
gross). Do not use remaining-unhedged as the denominator — that double-counts hedges
already in `current_hedge_ratio`. Remaining is still returned per CF for residual open.

Compose paging is capped (20 pages / 2000 rows); narrow with `entity_id` / `cash_flow_ids`
if the book is larger.

## Do not invent cash flows

If the user says "my book / this client":

1. `whoami`
2. `my_entities` (choose entity)
3. Page `my_cash_flows` (PROVIDER+)
4. Prefer `excel_hedge_from_book` (or `hedge_effectiveness` / `strategy_prior`) over
   hand-building `excel_hedge_compare` with guessed monthly notionals

`target_limit` remains caller input (foreign-currency accrual cash, not pips) —
see `tarf-proposal.md`.

Profile currencies on Entity are still landing upstream; until then, take a currency from
a cash flow when calling `my_risk_profile` / `strategy_prior`.
