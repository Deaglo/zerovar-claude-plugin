# FX Maestro

FX Maestro connects Claude to ZeroVaR for foreign-exchange analysis. It returns indicative rates, forward curves, and hedge comparisons for the account you sign in with. Maestro does not execute trades, and a result is not a quote.

## Account

You need a ZeroVaR account. Claude signs in with OAuth at platform.zerovar.com. This package does not contain an API key or a password.

Spot tools are available at CLIENT_VIEW. Forwards, volatility, cash flows, and hedge comparisons need PROVIDER or above. If a tool is refused, say so instead of filling in the missing number.

## Tools

This package connects to `https://api.zerovar.com/mcp`. Every tool below only reads. None of them writes a trade, a cash flow, or a booking. The QuickRisk connector is a different server and is not in this package.

Signed in (`whoami` is available to any authenticated caller):

- `whoami` — the caller's identity and access level. Read-only. Any signed-in user.

CLIENT_VIEW and above:

- `market_spot` — one live spot rate for a pair, with `as_of`. Read-only. CLIENT_VIEW.
- `market_currencies` — the currency list and each latest USD rate. Read-only. CLIENT_VIEW.
- `excel_spot` — the same spot, in the spreadsheet shape. Read-only. CLIENT_VIEW.
- `excel_tick` — bid, ask, and mid around a time. Read-only. CLIENT_VIEW.
- `excel_option_price` — premium and greeks for a vanilla, barrier, or American option. Read-only. CLIENT_VIEW.
- `analytics_carry` — annualised carry from a spot and a forward. Read-only. CLIENT_VIEW.
- `my_entities` — the caller's entities. Read-only. CLIENT_VIEW.
- `my_profile` — the caller's profile. Read-only. CLIENT_VIEW.

PROVIDER and above:

- `market_spot_history` — daily spot history for a pair. Read-only. PROVIDER.
- `market_spot_range` — last, min, and max spot over a window. Read-only. PROVIDER.
- `market_forward` — one forward tenor. Read-only. PROVIDER.
- `market_forward_strip` — the forward curve across calendar months. Read-only. PROVIDER.
- `market_volatility` — realised volatility from the trailing year of spot. Read-only. PROVIDER.
- `excel_tarf` — premium and greeks for one TARF ticket. Read-only. PROVIDER.
- `excel_tarf_strike` — the zero-cost TARF strike. Read-only. PROVIDER.
- `excel_tarf_program` — a TARF schedule, strike, and audit. Read-only. PROVIDER.
- `excel_hedge_compare` — percentiles for unhedged, a forward strip, and a zero-cost TARF. Read-only. PROVIDER.
- `excel_hedge_from_book` — that comparison built from the caller's cash flows. Read-only. PROVIDER.
- `my_cash_flows` — the caller's cash flows. Read-only. PROVIDER.
- `my_trades` — the caller's trades. Read-only. PROVIDER.
- `my_risk_profile` — the risk profile for an entity and currency. Read-only. PROVIDER.
- `hedge_effectiveness` — coverage of cash-flow notional by booked trades, residual, and tenor mismatch. Read-only. PROVIDER.
- `strategy_prior` — allowed products and the hedge policy from the risk profile. Read-only. PROVIDER.

## What you can ask

- Give me a market snapshot of EUR/USD, including spot and a 6-month forward.
- Compare an unhedged position with a forward and a zero-cost TARF for this exposure.
- Read my open cash flows and propose a hedge from the book.

## Support

Questions: support@zerovar.com

- https://zerovar.com
- https://zerovar.com/privacy
- https://zerovar.com/terms
