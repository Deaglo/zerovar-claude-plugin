# FX visualization guide

Deaglo charts are decision surfaces, not decoration. Hosted FX MCP returns compact JSON;
the host draws. Match the platform family to the question. Read this before inventing a
chart. TARF workflow rules stay in tarf-proposal.md.

## Hard rules

- "What is the level now" is a number plus as_of (optional 7-30 point sparkline), not a
  2-year chart.
- Compact first. Prefer last/min/max, percentiles, or a handful of tenors. Do not dump
  market_spot_history for a slide range - use market_spot_range.
- Do not invent charts the API did not produce. If we returned percentiles, show a violin
  / density / percentile table - never homemade 20k-path fans or knockout stats.
- Label the rate: mid vs client/spread-adjusted. Never call a spread-adjusted series
  "Spot Rate".
- ISO pair for the series (AUD/USD levels on AUD/USD). Sell/buy belongs in prose only.
- No pie, donut, treemap, candlestick, or 3D vol. The platform does not use them.
- Tables are first-class: forward strip, TCA, audit, 3-8 KPIs.
- Price action only unless the user supplied news.
- QuickRisk (/mcp/quickrisk) is provenance tables, not invented swap charts.

## Methods footer (every deliverable)

Close every analysis with Sources / What was done / Models (and Not a quote):

- Sources: tools called + as_of + source exactly as returned. No web quotes unless asked.
  If two calls return different as_of values, show both and do not present them as one
  market.
- What was done: those tools in order; name any 403 / CLIENT_VIEW skips.
- Models: live option and TARF pricing for premium and strike; Monte Carlo simulation for
  hedge-compare path stats; realised historical volatility for market_volatility; host prose
  is not a model.
- If audit[] is on the payload, paste it - that is the Methods table. Do not invent another.

## Scenario -> present as / do not

- Live rate: number + as_of + source. Not a historical candlestick.
- How did spot move: short date/value series or range + 1W/1M/1Y %. ValueGraph. No OHLC.
- Forward curve: tenor table + optional line (market_forward_strip). Not six market_forward
  calls. Omit on so the strip is the live market, the same quote the platform shows.
  Print the returned spot and each tenor's annualised carry (Paid or Received). Do not
  invent tenors or back a spot out of points. Pass on only for a stored historical curve.
- Hist vol: number + as_of. Not a vol surface.
- Outcome distribution / hedge compare: violin or percentile table from p05/p25/p50/p75/p95
  and P(meet budget). Never paths. Paste audit[].
- Hedged vs unhedged: two named percentile vectors (do_nothing vs tarf / forward_strip).
  Dual density, not one violin.
- Structure (option/TARF): legs + premium/greeks table. Payoff only if asked. No fan.
- Collar, seagull, call spread, or participating forward: not on this connector yet.
  Say so in the first paragraph. Do not price the legs with excel_option_price and do
  not attach a host-built fan. A single vanilla premium and its greeks stay on
  excel_option_price. Do not invent a fitted vol surface or a vol-reduction percent
  as a Deaglo number.
- Multi-client book / portfolio console: live spot / vol / strip / range only, or label
  the book and any correlated simulation as host fiction. No portfolio Monte Carlo tool.
- Book / waterfall / audit: table, not a cascade or pie.

## Per tool

Show only what the tool returned. Do not invent a chart this list does not name.

- `whoami`: kind and level_name. No chart.
- `my_profile`: kind and organization fields. No chart.
- `my_entities`, `my_cash_flows`, `my_trades`, `my_risk_profile`,
  `hedge_effectiveness`, `strategy_prior`, `market_currencies`: a table.
  No pie, heatmap, cascade, or chart. Do not invent rows the tool did not return.
- `analytics_carry`: basis points and percent. No chart.
- `market_spot`, `excel_spot`: a number plus as_of. A sparkline only if asked.
  No 2-year chart.
- `market_spot_history`: a ValueGraph time series. No candlesticks.
- `market_spot_range`: last, min, and max. A sparkline only if asked.
  No 2-year chart.
- `market_forward`: one tenor. No candlestick. Several tenors are
  `market_forward_strip`, not repeated calls.
- `market_forward_strip`: a tenor table, with a line only if it helps.
  Do not split it into separate forward calls.
- `market_volatility`: a number plus as_of. No volatility surface.
- `excel_tick`: bid, ask, and mid plus as_of, in the order returned.
  No candlestick.
- `excel_option_price`: a premium and greeks table. A payoff only if asked.
  No spot time series.
- `excel_tarf`: a premium and greeks table. No fan, and no path statistics.
- `excel_tarf_strike`: a strike table. No fan.
- `excel_tarf_program`: the schedule, the strike, and the audit table. No fan.
- `excel_hedge_compare`, `excel_hedge_from_book`: a violin or percentile table,
  plus audit when the result includes it. No paths. Do not invent cash flows
  for the book tool.

## Payload shapes we already return

- Level: pair, side, rate, as_of, source
- Range: last, min, max, points, as_of
- Strip: live spot and side, tenors[{months, bid, mid, ask, spot, annualised_carry_pct,
  carry_label}], average_mid. A historical on uses horizon_weeks and has no live spot.
- Compare: fan[], do_nothing / forward_strip / tarf percentiles, audit[], no paths
- Provenance: audit[] is part of the answer - paste it

Legacy connector PNGs (candlestick / fan) are a different product. Do not treat them as
the platform convention.
