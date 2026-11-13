---
name: ship-order
description: Ship a CerebroChain order. Pull the order, compare live carrier rates, and prepare the label for the user to buy in the console. Use when the user asks to ship or send out an order, get a label for an order, or find out what an order costs to ship.
---

# Ship an order

Carry one order from "ready" to a prepared label. The user buys the label themselves in the console
with one click; nothing in this skill spends their money.

## 1. Find the order

Call `get_order` with the order ID. If the user gave a customer name or a partial ID, call
`list_orders` with it as the search and confirm which order they mean.

Read the ship-to address from the order. A label needs the full address: name, street, city, state
and ZIP. An order can be saved without a ship-to address; when the address or any part of it is
missing, ask the user for it.

Labels cover US domestic shipments. For an address in another country, quote the lane for comparison
and tell the user the label is bought with the carrier directly.

## 2. Check what was packed

For an order at PICKED or PACKING, call `get_pick_list` with stage "pack" and the order ID. It shows
the quantities actually picked. If a line was picked short, tell the user before quoting, since the
package holds less than the order lists. For an order at any other status, go on to step 3.

## 3. Collect the package details

The order does not hold these, so ask for them once, together:

- Package weight in pounds, and length, width and height in inches.
- The ship-from address: name, street, city, state and ZIP.
- The sender's phone number or email. USPS requires one to sell a label.
- Whether the destination is a home, and whether a signature is required. Carriers price both into
  every rate.

Use the user's numbers. Never estimate a weight.

## 4. Quote once, with everything

Call `compare_shipping_rates` with the full from and to addresses and the package details. Each
quote is a billed call, so make one complete call instead of several partial ones. A prepared label
prints exactly the addresses its rate was quoted with.

Present three options in a small table: the cheapest, the fastest, and the best value, each with
carrier, service, delivery estimate and the all-in price. The all-in price is the carrier's price
plus the $1.00 label fee, which is what the user pays. Give the dollar difference between cheapest
and fastest.

Keep each option's rate ID with it. Carriers quote the same service name at more than one price, and
the reply flags it when they do, so the rate ID is what identifies the option the user picked. Show
delivery estimates as the reply gives them.

## 5. Prepare the label

When the user picks an option, call `prepare_label` with that option's rate ID and the order ID as
the reference.

Then tell the user, in these terms:

- The label is saved and ready. Nothing has been bought or charged.
- Here is the console link. Review it there and click Buy.
- The price comes out of the account's prepaid credits, and the carrier's price is rechecked at the
  moment of purchase.

If the user says the console shows too few credits to buy, call `buy_credits` and pass along the
checkout link it returns. A checkout link is not a payment; credits arrive once the user completes
it.

## 6. Close the loop

After the user says they bought the label, call `list_shipments` and find it by the order ID
reference. A status of "confirming" means the carrier has not confirmed the purchase yet; check
again in a few minutes.

Once the label shows as created, call `get_order`. If the order is not yet at SHIPPED, ask the user
whether to mark it shipped, and say that shipping takes the order's units off on-hand stock and what
the call costs. On a
yes, call `update_order_status` with SHIPPED. If the move is refused, relay the reason it gives,
such as lines still open on the Picking page.

Finish with the carrier, the service, the reference and the order's new status.

## If a call is refused

A 402 has one of two causes, and the refusal says which: the plan or trial has lapsed, or the credit balance is below that call's price. Call `get_account_status` for the plan and the balance. For a lapsed plan, relay the upgrade link. For low credits, call `buy_credits` and pass along the checkout link; credits arrive once the user completes payment.
