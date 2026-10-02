---
name: fx-analysis
description: Run FX analysis with the ZeroVaR connector. Use for spot, forwards, a client book, TARF or hedge comparisons, and NDF close-outs. Do not invent rates or notionals.
---

# FX analysis

This skill uses the FX connector at `https://api.zerovar.com/mcp`. QuickRisk at `/mcp/quickrisk` is interest-rate swaps only. Do not look there for FX.

Use the MCP tools. Do not web search for rates. Rates are indicative, not executable quotes. Maestro does not execute trades.

Call `whoami` before a workflow that depends on access level. CLIENT_VIEW does not include forwards or TARF. Those tools need PROVIDER or above. If a tool returns 403, say that it was skipped.

For "my book", read `fx://book-guide` after sign-in. Page `my_cash_flows` (PROVIDER or above) and prefer `excel_hedge_from_book`. Do not invent notionals.

Forward curves often lag spot by one or more days. If `market_forward` or `market_forward_strip` returns no forward curve for `on` (the default is today), retry with `on` set to prior business days, up to about 5–7. Cite the `on` and the side (bid, mid, or ask) that worked. Do not invent strip points, mix a spot from another date, or change the carry convention. Use `analytics_carry` on the same forward and spot.

Never name a third-party pricing vendor. Say live option and TARF pricing.

After sign-in, read the server resource that matches the task instead of guessing the workflow:

- `fx://viz-guide` before drawing
- `fx://tarf-proposal-guide` before a client pitch (`target_limit` is foreign cash, not pips)
- `fx://ndf-closeout-guide` before an early NDF or forward close-out
- `fx://book-guide` for the client's own cash flows and trades

Close every analysis with a short Methods footer:

- Sources: the tools you called, plus `as_of` and `source` exactly as returned. Do not cite web pages unless the user asked for them.
- What was done: those tools in order. If a tool was skipped, say so.
- Models: live option and TARF pricing for premium and strike; Monte Carlo simulation for `excel_hedge_compare` and `excel_hedge_from_book` path stats; realised historical volatility for `market_volatility`. The host narrative is not a pricing model.
- Not a quote.

For a TARF or hedge proposal, if the result includes `audit[]`, paste that table as the Methods section. Do not invent a second one.
