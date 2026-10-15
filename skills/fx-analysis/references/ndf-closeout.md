# NDF / forward early close-out guide (FX MCP)

Use this before building a client unpack of an early unwind. Do not invent discount rates,
path fans, or Bloomberg prints. This surface has spot, forward bid/mid/ask, carry, and
realised vol - not a money-market curve lookup and not a 25-delta skew surface.

## Required ticket fields (caller / confirmation)

- Pair, notional (base), client direction (buys vs sells base)
- Trade date and maturity (or residual horizon in weeks)
- Contract strike from the confirmation - never reconstruct from a historical offer unless
  the client still has not sent the ticket (and label it ASSUMED)
- Settlement currency and type: NDF vs deliverable; PV today vs settle at original maturity
- Confirm no early-termination fee and the bank's close-out convention

## Market snapshot (tools)

PROVIDER+: `market_spot` (omit side for MID), `market_forward` or `market_forward_strip`
for the residual horizon (bid / mid / ask). `analytics_carry` is a CIP cross-check from
spot and forward mid - not the discount factor.

CLIENT_VIEW cannot see forwards; say PROVIDER+ is required. Do not web-search a forward.

## Bid vs offer for the offsetting trade

- Client long base (bought USD) closing out -> sells base forward -> use the bank's
  **bid** as the close-out rate.
- Client short base closing out -> buys base forward -> use the bank's **ask**.
- Mid isolates spread only; never call a bid or ask "Spot Rate".

Settlement at maturity (undiscounted), for long base closed on the bid:

  COP_undisc = (F_bid - K) * N
  USD_undisc = COP_undisc / F_bid   (only if converting at the close-out forward)

Sign: negative rate difference means a loss to a long-base client when F < K.

## Discount factor (do not invent)

There is **no** COP or USD money-market tool on this MCP. Bloomberg (or the desk curve)
is a **required blue input** with ticker / source. Label it ASSUMPTION until checked.

Do **not**:

- Hard-code 10% (or any) COP rate from "carry plus a USD base" without labelling both
  legs as assumptions
- Treat `analytics_carry` or (F/S - 1)/t as the DF itself - that is a cross-check only
- Mix QuickRisk IR discount curves into an FX NDF close-out

Simple money-market DF (act/360), when the desk supplies i_COP:

  DF = 1 / (1 + i_COP * days / 360)
  PV_COP = COP_undisc * DF

USD conversion: if settlement is COP then convert PV at **spot mid**, say so. If the NDF
settles in USD, PV in USD with a USD DF - do not silently mix F_bid for undiscounted USD
and spot for PV USD without stating the convention. Make settlement currency an input.

## Self-check block (must all pass)

Before showing a headline cash-out number, verify:

1. days = maturity - valuation
2. COP_undisc = (F_close - K) * N
3. USD_undisc = COP_undisc / F_close (when that conversion applies)
4. DF = 1 / (1 + i * days / daycount)
5. PV_local = COP_undisc * DF
6. PV_USD_spot = PV_local / S_mid (when converting at spot)
7. F_bid <= F_mid <= F_ask
8. Recomputed carry from analytics_carry(S, F_mid, residual weeks) vs (F_mid/S - 1)/t

If any check fails, fix the model - do not ship the pack.

## Residual risk vs cash-out (second slide)

Unwinding removes the hedge. If the underlying exposure still exists, the client holds
open notional for the residual tenor. Compare |cash-out PV| to residual risk - do **not**
invent a 20,000-path fan.

What we have today:

- `market_volatility` - realised historical vol (251 sessions)
- Parametric 95% loss on notional over residual tenor from that vol (state the formula)
- Optionally one ATM `excel_option_price` implied vol as a second column if the live pricer prices
  the pair - if it fails, say so

What we do **not** have: a dedicated VaR95 NDF tool, or risk-reversal / 25d skew. Forward
bid/ask is spread, not skew. Do not tell the client we "have skew" unless you price at
least a 25d call and 25d put via excel_option_price and report RR = IV_call - IV_put with as_of.

## Methods footer

Sources (tools + as_of + source exactly as returned; DF source = caller / desk / BBG), What was done
(tools in order; name ASSUMED ticket fields), Models (simple MM DF; realised vol for
parametric residual risk; host narrative is not a model), Not a quote.

## Do not

- Invent discount rates or scrape Bloomberg through the host
- Reconstruct strike from a historical offer without ASSUMED
- Skip the self-check sheet
- Use excel_hedge_compare path stats as NDF close-out residual VaR
- Point this workflow at /mcp/quickrisk
