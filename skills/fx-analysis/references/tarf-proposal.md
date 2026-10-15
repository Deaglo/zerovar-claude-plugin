# TARF proposal guide (FX MCP)

Use **one** workflow call for a client hedge pitch. Do not invent path statistics.

This surface is the **FX** catalogue at `/mcp`. QuickRisk (`/mcp/quickrisk`) is interest-rate
swaps only - do not look there for Monte Carlo or TARF path stats.

## Preferred tool

`excel_hedge_compare` - compares a zero-cost TARF with unhedged and forward alternatives
using consistent market inputs. It returns compact percentiles plus P(meet budget).
Optional `on` pins a stored historical curve. When omitted, the tool uses the live
market, the same quote the platform shows. Print `market.spot` from that quote. Do not
back a spot out of forward points. An explicit `on` is not walked past.

If you only need the strike and market snapshot (no simulation), call `excel_tarf_program`.

## Audit (paste into the deck)

Every compare / program result includes an `audit` array: `field`, `tool`, `origin`, `note`.
Paste it into the sales document - that **is** the Methods / Sources table for this
deliverable. Do not invent a second Methods section beside it. Preserve every audit
field, origin, tool and note exactly as returned.

Also report `method` and `disclaimer` from `excel_hedge_compare`, and
`enhancement_vs_strip` (= strike - strip average). Positive does **not** always mean
"better for the client" - for `SELLS_BASE`, a strike below the forward strip is worse
than locking the strip.

## client_side (do not set is_bought by hand)

- `SELLS_BASE` - client sells the base (e.g. sells USD in USD/MXN). Meet-budget = average
  rate **>=** budget. Prices the sold structure (`is_bought=false`).
- `BUYS_BASE` - client buys the base. Meet-budget = average rate **<=** budget. Prices
  `is_bought=true`.

`monthly_notional` is per fixing in the **base** currency. `target_limit` is the pricing
service's accrual knockout in **foreign-currency cash** (the same family of units the simulation
uses: notional x |spot - strike|), **not** rate points or pips.

### Worked EUR/USD example (do not send "500 points")

Client sells EUR / buys USD, **USD 1,000,000** per month x **12** months, wants ~2 cents
of strike enhancement vs the forward strip. Sales language "500 points" means **500 pips
of FX rate**, which is **not** a `target_limit`.

- Wrong: `target_limit=500` (or `0.05`, or `0.005`) — the bucket is tiny vs
  `monthly_notional`, so knockout cannot bind; premiums look almost unchanged
  across nonsense targets.
- Right order of magnitude: foreign accrual cash on the order of **hundreds of thousands
  to millions of USD** for this notional (e.g. a few million USD), analogous to the
  USD/MXN sales examples that use `target_limit=3000000` with `monthly_notional=500000`
  or `1000000`. Confirm the exact cash bucket with the desk; do not guess from pips.

`excel_tarf_program` / `excel_hedge_compare` reject targets that are far below
`monthly_notional` or far above `monthly_notional x months` (knockout cannot bind). Prefer
**one** `excel_hedge_compare` call over four raw `excel_tarf` prices at guessed targets.

## Do not

- Call `excel_tarf` after a zero-cost `excel_tarf_strike` / program / compare - the strike
  is already solved at zero cost; a second pricing call is wasted load.
- Dump two years of `market_spot_history` for a range slide - use `market_spot_range` or
  the `market.range_2y` field on the compare result.
- Call `market_forward` six times - use `market_forward_strip` or the compare result.
- Invent 20,000-path fan charts, knockout probabilities, or budget hit rates. If
  `excel_hedge_compare` is unavailable at your auth level, say so. For what to draw,
  read `viz.md`.
- Restart the whole compare after a strike timeout - retry `excel_tarf_strike` alone.
- Run a host Monte Carlo for a collar, seagull, call spread, participating forward, or
  fitted vol surface. Those structures are not on this connector yet. Say so in the first
  paragraph. Do not price the legs with `excel_option_price` and do not attach a host-built
  fan. `excel_hedge_compare` is only unhedged, the forward strip, and a zero-cost TARF.
- Treat an invented multi-client book (correlated GBM, credit, client vol reduction %) as
  ZeroVaR output. Live G4 spot / vol / strip / range is fine; label the book hypothetical.

## Timeouts

Strike solve is the slow step (often 15-50s). Routes fail before the ~60s ALB idle cut.
The simulation itself is local and compact; responses never include a `paths` array.
