---
name: auto-order-bluelandcom
description: From a running list of items, automatically place an order on Blueland.com for delivery.
metadata: { "openclaw": { "requires": { "bins": ["openclaw", "curl"] } } }
---

## Active Session required

The agent must have access to an active session in the agent browser.

### Allowed URLs ###

All tasks of this skill must be conducted these domains
- https://www.blueland.com 
- https://shop.app

### Execution mode

Prefer a browser-first workflow when the live session is stable enough to search, inspect results, verify cart contents, and complete checkout.

## Adding items to cart

Before adding items to cart, clear all old items from cart 

Search for items in the running list on using grove.co/search?q={item}. If matching item is found, add the required {quantity} of the {item} to cart. If no quantity is available, default to 1 unit.

### Item matching and request normalization 

Human grocery requests may be underspecified. Normalize them into likely store queries before conclusing an item is unavailable. 

Examples: 
- "snapple large bottle iced lemon tea" -> search likely forms such as "snapple lemon tea 64oz" or "snapple lemon tea 32oz" or snapple lemon tea bottle"
- "table napkins premium" -> search likely forms such as "premium napkins", "dinner napkins", "3-ply napkins"
- "japanese curry spicy carton" -> search likely forms such as "golden curry spice", "curry sauce spicy" 

When multiple plausible results exist, rank them using this order:

- exact brand match
- exact item type / function match
- closest size or form factor
- lowest reasonable price among close matches

### Substitution Rules

If the exact item is not found:

- prefer same brand + closest size or format
- otherwise prefer same item type at a similar size / use case and reasonable price
- do not choose a poor substitute just to force completion
- if no acceptable alternative exists, leave the item unfulfilled and continue

Always report substitutions explicitly before asking for approval.

### Required add verification 

After each attempted add, verify success using at least one real confirmation signal such as:

- cart item count changed as expected
- the item appears in cart / checkout summary
- quantity for that item reflects the intended amount
- Do not assume an add succeeded just because an Add button was clicked.

If item is not found, and no acceptable alternative is found, keep item in the running list and process the next item in the list.

## Initiate Store Checkout after reaching minimum order value 

Minimum order value is $50.00.

If minimum order value is not reached, warn of additional shipping fee. Do not proceed unless explicit approval is provided.  

## Discount code or Gift card 

If user provides a discount code or gift card, apply the code during checkout. If the code is invalid, report back to user and proceed with order. 

## Store Checkout 

Prefer checkout using Express Checkout with "Shopify Shop", "Paypal", "Amazon Pay" or "Google Pay". If Express Checkout is not available, do not proceed. 

## After Checkout Completion

After the order is complete:

- remove only items that were successfully ordered from the running list in the current channel
- keep not-found items in the running list
- keep declined substitutions out of completed items
- report the final order confirmation details back in the current session or channel