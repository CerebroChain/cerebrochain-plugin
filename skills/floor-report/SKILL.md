---
name: floor-report
description: Build a daily warehouse floor report from CerebroChain covering what to pick and pack, stuck orders, receiving backlog, stock at risk and KPIs. Use when the user asks for a morning report, a shift handoff, a daily operations summary, or what needs attention in the warehouse today.
---

# Daily floor report

One page a warehouse lead can read in two minutes and act on. This skill reads; it changes nothing
in the account.

## 1. Gather

Make these calls:

1. `get_pick_list` with stage "pick": orders waiting to be picked, oldest first, with SKU, quantity
   and bin.
2. `get_pick_list` with stage "pack": orders picked and ready to pack, with ship-to and shipping
   method.
3. `detect_bottlenecks`: orders stuck in a status, receiving not yet put away, and SKUs at or below
   their reorder point. The default 30-day window suits a daily report.
4. `get_order_statistics`: order totals by status and by day.
5. `get_kpi_status`: the account's KPIs over the last 30 days, each with its trend.

Calls are paid from the account's prepaid credits, and each tool states its price. The first time the user asks for this report, tell them what one run costs, added up from those prices, before making the calls.

## 2. Write the report

Use this order, and leave out any section that has nothing in it:

**Today in three lines.** The three things that most need a person, most urgent first. Name the
order IDs or SKUs.

**Pick now.** Number of orders waiting, the oldest order and how long it has waited, and total lines
to pick.

**Pack now.** Orders ready to pack. Call out any line picked short.

**Stuck.** Each bottleneck finding: where, how many, how long against the threshold, the sample IDs,
and the recommendation the tool gives.

**Receiving.** Receipts not put away within 24 hours.

**Stock at risk.** The SKUs the bottleneck check reports with recent demand and free stock at zero or at the reorder point, zero first. Show free stock against the reorder point. For the full low-stock list, offer `query_inventory` as a follow-up.

**Numbers.** Orders by status and the KPIs, in one compact table. Leave out statuses with no orders
and KPIs that come back with no value. The KPIs carry no targets, so report each value and its trend
without grading it.

## 3. Hold to the data

- Every figure comes from a tool reply. Do not estimate, round up a trend, or fill a gap.
- When a tool answers that there is too little history, say that the account needs more order
  history for that section and move on.
- Cite the order ID or SKU beside each claim so the reader can open it in the console. Include the
  console links the tools return.
- Flagged pick lines, such as a SKU with no inventory record or stock past its expiry date, go in
  the three lines at the top.

## 4. Offer the next step

End with at most two offers that fit what the report found, for example shipping the oldest packed
order or reviewing what to reorder. Wait for the user to choose; do not start either one unasked.

## If a call is refused

A 402 has one of two causes, and the refusal says which: the plan or trial has lapsed, or the credit balance is below that call's price. Call `get_account_status` for the plan and the balance. For a lapsed plan, relay the upgrade link. For low credits, call `buy_credits` and pass along the checkout link; credits arrive once the user completes payment.
