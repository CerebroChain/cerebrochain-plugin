# CerebroChain

Run your warehouse from a conversation with Claude. This plugin pairs the CerebroChain connector
with five skills that carry everyday warehouse jobs to the point where you review and approve.

## What it does

- **Set up a workspace.** Attach your item spreadsheet or a system export and say "set us up."
  Claude maps the columns, shows you what will load, and brings in your warehouse, items and opening
  counts.
- **Ship an order.** Claude pulls the order, compares live UPS, USPS and FedEx rates, and prepares
  the label. You buy it in the console with one click.
- **Daily floor report.** What to pick and pack, stuck orders, receiving backlog, stock at risk and
  KPIs on one page.
- **Reorder review.** A buy list built from your own demand and stock, with stockout dates, reorder
  points, dead stock and overstock.
- **Rate shop a package.** Cheapest, fastest and best-value options for any package, with all-in
  prices.

## Use it

1. Install the plugin and connect the CerebroChain connector on the plugin's Connectors tab. You
   sign in with your CerebroChain account; create one at
   [app.cerebrochain.com](https://app.cerebrochain.com).
2. Ask in plain words:
   - "Set us up from this spreadsheet."
   - "Ship order ORDER-1042."
   - "Give me this morning's floor report."
   - "What should I reorder? My supplier takes 10 days."
   - "What does a 12 lb box cost to send from 89501 to 30301?"

Claude asks before it changes anything in your account. Labels and credit packs are bought by you,
in the console or in Stripe Checkout, never inside a conversation.

## Data

The plugin contains instructions only. It runs no code and stores nothing. Its skills call the
CerebroChain connector at `https://cerebrochain.com/mcp`, which reads and writes your own
CerebroChain workspace after you sign in. Rate quotes send the package details and addresses you
provide to the carriers through CerebroChain's shipping provider. Nothing is sent anywhere else.

## Pricing

Workspace tools run on a CerebroChain plan or trial. Each tool call is paid from prepaid credits at
the price that tool states: a few cents for a read, more for an analysis or a change to your
account. Claude tells you what a report or a load costs before it runs. Rate quotes work on credits
alone, with no plan. A label costs the carrier's price plus a $1.00 label fee.

## Support

- Setup guide: [cerebrochain.com/connect](https://cerebrochain.com/connect)
- Privacy: [cerebrochain.com/privacy](https://cerebrochain.com/privacy)
- Help: support@cerebrochain.com
