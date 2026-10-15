---
name: fx-analysis
description: Run FX analysis with the ZeroVaR connector. Use for spot, forwards, a client book, TARF or hedge comparisons, and NDF close-outs. Do not invent rates or notionals.
---

# FX analysis

This skill uses the FX connector at `https://api.zerovar.com/mcp`. QuickRisk at `/mcp/quickrisk` is interest-rate swaps only. Do not look there for FX.

Use the MCP tools for rates, curves, cash flows, and trades. Do not web search for rates. Rates are indicative, not executable quotes. Maestro does not execute trades.

Call `whoami` before a workflow that depends on access level. CLIENT_VIEW does not include forwards or TARF. Those tools need PROVIDER or above. If a tool returns 403, say that it was skipped.

For "my book", follow `references/book.md` in this package. Page `my_cash_flows` (PROVIDER or above) and prefer `excel_hedge_from_book`. Do not invent notionals.

Omit `on` on `market_forward` and `market_forward_strip` for the live market, the same quote the platform shows. Print the returned spot. Do not back a spot out of forward points, and do not pass `on` to fetch an older stored curve and call it current. Pass `on` only for a historical stored curve. An explicit `on` is not walked past. If two calls return different `as_of` values, show both and do not present them as one market. `analytics_carry` is the week cross-check for a historical pair of rates, not a substitute for the live strip.

If asked where a price comes from, say a licensed pricing provider. Do not invent a firm's name. Say live option and TARF pricing, including in any subtitle or button you draft.

Follow the file in this package that matches the task. Do not read workflow guides from the server.

- `references/viz.md` before drawing. That file lists the presentation for each tool: a number plus `as_of`, a tenor table, a premium table, or a violin. Do not invent a chart the file does not list.
- `references/tarf-proposal.md` before a client pitch (`target_limit` is foreign cash, not pips)
- `references/ndf-closeout.md` before an early NDF or forward close-out
- `references/book.md` for the client's own cash flows and trades

Close every analysis with a short Methods footer:

- Sources: the tools you called, plus `as_of` and `source` exactly as returned. Do not cite web pages unless the user asked for them. If two calls return different `as_of` values, show both and do not present them as one market.
- What was done: those tools in order. If a tool was skipped, say so.
- Models: live option and TARF pricing for premium and strike; Monte Carlo simulation for `excel_hedge_compare` and `excel_hedge_from_book` path stats; realised historical volatility for `market_volatility`. The host narrative is not a pricing model.
- Not a quote.

For a TARF or hedge proposal, if the result includes `audit[]`, paste that table as the Methods section. Do not invent a second one.
