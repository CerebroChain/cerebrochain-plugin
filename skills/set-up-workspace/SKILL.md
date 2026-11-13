---
name: set-up-workspace
description: Set up a CerebroChain workspace from a spreadsheet or a system export. Use when the user says "set us up", wants to add a warehouse, load items, SKUs or opening stock, or move inventory from a spreadsheet, an ERP, an accounting system or a store into CerebroChain.
---

# Set up a CerebroChain workspace

Take the user from an empty account to a warehouse with their real items and counts in it. Every
write waits for the user's go-ahead, and every figure loaded comes from the user's own file.

## 1. Check the account

Call `get_account_status`. It reports the workspace, the plan or trial window, and which tools are
open.

- If writes are gated, tell the user what the status says and give the upgrade link it returns. Stop
  here until the account is active.
- If the account holds the labeled sample warehouse, offer `remove_demo_data` before loading real
  items, so sample rows and real rows never mix.

## 2. Make sure a warehouse exists

Call `list_warehouses`.

- If the user's warehouse is listed, note its short code. Items point at a warehouse by that code.
- If none exists, ask for the warehouse name, a short code (for example RNO-1) and the street
  address. Read the three back, and on a yes call `create_warehouse`.

## 3. Read the item file

Ask the user to attach or paste their item list: a spreadsheet, a CSV, or an export from the system
they use today. Then map its columns onto the item fields:

| Field                          | Required | What it holds                                  |
| ------------------------------ | -------- | ---------------------------------------------- |
| sku                            | yes      | The match key, up to 64 characters             |
| name                           | yes      | Item name                                      |
| on_hand                        | no       | Physical count, a whole number. Sets the stock |
| warehouse                      | no       | Warehouse short code from step 2               |
| zone                           | no       | Zone inside the warehouse                      |
| category, description, barcode | no       | Catalog details                                |
| reorder_point                  | no       | Low-stock threshold                            |
| weight, length, width, height  | no       | Unit weight and dimensions                     |

Ask which units the file uses for weight and dimensions. Pounds and inches match what the rate tools
take.

Never fill a blank with a guess. A missing count stays missing, and that item loads as a catalog
row.

## 4. Show the plan before writing

Show the user:

- The column mapping, one line per field.
- The row count, and the first five rows as they will load.
- Every row that needs a fix, with the reason: no SKU, no name, a SKU that appears twice, a count
  that is negative or not a whole number.

State two things plainly: a SKU already in the account is updated, not duplicated, and an on_hand
value replaces that item's count with the number in the file.

Calls are paid from the account's prepaid credits, and each tool states its price. Include the cost of the load in the plan: one import call per 500 rows.

Wait for a yes.

## 5. Load the items

Call `import_items` with at most 500 rows per call, and send larger files in batches. The reply
lists the rows that failed and why. Report created, updated and failed counts after each batch. Fix
failed rows with the user and send only those rows again.

## 6. Confirm and hand off

Call `query_inventory` and check the item count and a few on-hand figures against the file. Give the
user the console inventory link from the reply.

Close with the next step that fits: the first order (`create_order`), or a rate quote for a package
they need to send today.

## If a call is refused

A 402 has one of two causes, and the refusal says which: the plan or trial has lapsed, or the credit balance is below that call's price. Call `get_account_status` for the plan and the balance. For a lapsed plan, relay the upgrade link. For low credits, call `buy_credits` and pass along the checkout link; credits arrive once the user completes payment.
