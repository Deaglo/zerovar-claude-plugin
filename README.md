# FX Maestro

FX Maestro connects Claude to ZeroVaR for foreign-exchange analysis. It returns indicative rates, forward curves, and hedge comparisons for the account you sign in with. Maestro does not execute trades, and a result is not a quote.

## Account

You need a ZeroVaR account. Claude signs in with OAuth at platform.zerovar.com. This package does not contain an API key or a password.

Spot tools are available at CLIENT_VIEW. Forwards, volatility, cash flows, and hedge comparisons need PROVIDER or above. If a tool is refused, say so instead of filling in the missing number.

## What you can ask

- Give me a market snapshot of EUR/USD, including spot and a 6-month forward.
- Compare an unhedged position with a forward and a zero-cost TARF for this exposure.
- Read my open cash flows and propose a hedge from the book.

## Support

Questions: support@zerovar.com

- https://zerovar.com
- https://zerovar.com/privacy
- https://zerovar.com/terms
