---
name: reorder-review
description: Review what to reorder in CerebroChain and draft a buy list from the account's own demand and stock. Use when the user asks what to reorder, what is running low, when an item will run out, how much to buy, what their reorder points should be, or about dead stock and overstock.
---

# Reorder review

Turn the account's order history into a buy list the user can send to their supplier. The figures
are statistics computed from the user's own orders and stock.

## 1. Ask for the two assumptions

Supplier lead time is the user's knowledge, not the account's. Ask:

- Lead time in days, from placing an order with the supplier to stock on the shelf.
- How much protection against running out they want: 90%, 95% or 98%. Higher means more safety
  stock.

If the user has no answer, use 7 days and 95%, and say in the report that both are assumptions.

## 2. Gather

1. `get_optimization_recommendations` with the lead time and service level: reorder points, items to
   reorder now, dead stock and overstock, each with its evidence.
2. `forecast_demand` for the 25 items with the most demand, or for one SKU when the user asked about
   one. It gives average daily demand, days of cover and the projected stockout date.

Calls are paid from the account's prepaid credits, and each tool states its price. Tell the user what the review costs, added up from those prices, before making the calls. When the user only asks whether one item is running low, `query_inventory` for that SKU answers it for the price of a read.

## 3. Write the buy list

**Reorder now.** A table, soonest stockout first:

| SKU | Item | Free stock | Daily demand | Runs out | Buy |
| --- | ---- | ---------- | ------------ | -------- | --- |

Take the Buy figure from the recommendation's action. Where the tool gives a reorder point and no
quantity, show the shortfall (reorder point minus free stock) and label it as the minimum to buy.

**Reorder points to change.** Items whose computed reorder point differs from the stored one:
stored, computed, and the demand behind it.

**Dead stock.** On hand with no demand in the window, with its value when cost is known.

**Overstock.** More than 180 days of cover, with the excess units.

State the lead time and service level used at the top of the list.

## 4. Hold to the data

- An item with too little history comes back with no forecast and a reason. List those items
  separately as "needs more history" and give no number for them.
- The forecast carries an 80% interval and no seasonality. Say so once, and remind the user to
  adjust for a known seasonal peak.
- Do not blend in outside knowledge about the product or the market.

## 5. Offer to save the new reorder points

If the user wants the computed reorder points stored, list each SKU with its old and new value and
wait for a yes. Then call `import_items` with each item's SKU, name and new reorder point, and no
on-hand count, so stock levels stay untouched.

## If a call is refused

A 402 has one of two causes, and the refusal says which: the plan or trial has lapsed, or the credit balance is below that call's price. Call `get_account_status` for the plan and the balance. For a lapsed plan, relay the upgrade link. For low credits, call `buy_credits` and pass along the checkout link; credits arrive once the user completes payment.
