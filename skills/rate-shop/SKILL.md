---
name: rate-shop
description: Compare live UPS, USPS and FedEx rates for a package and recommend the best value. Use when the user asks what a package costs to ship, which carrier is cheapest or fastest, or wants a shipping quote that is not tied to an order.
---

# Rate shop a package

Answer "what does this cost to ship, and which option should I take" from live carrier quotes.
Quotes run on prepaid credits and need no plan.

## 1. Collect the package once

Ask for everything in one message:

- Origin ZIP and destination ZIP.
- Weight in pounds, and length, width and height in inches when the user has them. Carriers bill
  large light boxes by size, so dimensions change the price.
- Whether the destination is a home, and whether a signature is required.

Ask whether the user plans to buy the label. If so, collect the full from and to addresses now
(name, street, city, state, ZIP, and the sender's phone or email), because a label prints the
addresses its rate was quoted with.

## 2. Quote

Call `compare_shipping_rates` once with everything collected. Each call is billed, so avoid quoting
twice for details that could have been asked up front.

## 3. Recommend

Show a table with the cheapest option, the fastest option and the best value: carrier, service,
delivery estimate and all-in price. The all-in price is the carrier's price plus the $1.00 label
fee.

Keep each option's rate ID with it. Carriers quote the same service name at more than one price, and
the reply flags it when they do, so the rate ID is what identifies an option. Show delivery
estimates as the reply gives them.

Then give a one-sentence recommendation with the reason and the dollar difference, for example:
"Ground at $14.20 arrives Thursday; two-day costs $9.85 more and saves one day."

When the user ships this lane often, offer to quote a second weight or box size so they can see
where the price steps up.

## 4. If the user wants the label

For a US domestic shipment quoted with full addresses, call `prepare_label` with the chosen option's
rate ID. Hand over the console link and say plainly that nothing has been bought: the user reviews
the label there and clicks Buy, paid from prepaid credits.

If the quote used ZIP codes only, quote again with the full addresses first, and tell the user why.

For an international lane, the quote is for comparison, and the user books that shipment with the
carrier.

## If a call is refused

A 402 on a quote means the credit balance is below the call's price. Call `get_account_status` for the balance, then `buy_credits` for the list of credit packs. Pass along the checkout link; credits arrive once the user completes payment.
