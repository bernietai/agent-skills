---
name: auto-order-instacart
description: From a running list of items, automatically place an order on Instacart for pickup or delivery.
metadata: { "openclaw": { "requires": { "bins": ["curl", "openclaw"] } } }
---
# Active session required

To place an order on Instacart, the agent must have access to an active session in the browser.

## Execution mode

Prefer a browser-first workflow when the live Instacart session is stable enough to search, inspect results, verify cart contents, and complete checkout.

If browser interaction is flaky, ambiguous, or repeatedly fails to confirm state changes, fall back to the same active session's Cookie header and use GraphQL requests to search, add items, inspect cart state, and complete checkout.

Do not mix guessed state with real state. After every meaningful action, verify what actually happened.

# Adding items to a Store Cart

From a RUNNING LIST of items in the current channel, manage multiple carts for different stores on Instacart. From the agent's memory, determine the preferred BRAND and STORE before adding the item to the cart. Search for a store using URL format: https://www.instacart.com/store/s?k={store_name}. If the store is found, select that store.

Do not select stores that are more than 10 miles from the delivery address.

Search the item using URL format: https://www.instacart.com/store/{store}/s?k={item}. If the BRAND is known, include brand using URL format: https://www.instacart.com/store/{store}/s?k={brand}+{item}.

If the item is found, add the required {quantity} of the {item} to cart. If no quantity is available, default to 1 unit.

## Item matching and vague request normalization

Human grocery requests may be underspecified. Normalize them into likely store queries before concluding an item is unavailable.

Examples:
- `snapple large bottle iced lemon tea` -> search likely forms such as `snapple lemon tea 64 oz`, `snapple lemon tea 32 oz`, `snapple tea lemon bottle`
- `table napkins premium` -> search likely forms such as `premium napkins`, `dinner napkins`, `3-ply napkins`
- `japanese curry spicy carton` -> search likely forms such as `golden curry spicy`, `japanese curry`, `curry sauce spicy`

When multiple plausible results exist, rank them using this order:
1. exact brand match
2. exact item type / function match
3. closest size or form factor
4. lowest reasonable price among close matches

## Substitution rules

If the exact item is not found:
1. prefer same brand + closest size or format
2. otherwise prefer same item type at a similar size / use case and reasonable price
3. do not choose a poor substitute just to force completion
4. if no acceptable alternative exists, leave the item unfulfilled and continue

Always report substitutions explicitly before asking for approval.

## Required add verification

After each attempted add, verify success using at least one real confirmation signal such as:
- cart item count changed as expected
- the item appears in cart / checkout summary
- quantity for that item reflects the intended amount

Do not assume an add succeeded just because an Add button was clicked.

If item is not found, and no acceptable alternative is found, keep item in the running list and process the next item in the list.

# Initiate Store Checkout after reaching minimum order value

On Instacart, separate these concepts clearly:
- store minimum needed to place an order
- threshold required for free delivery
- temporary discounts, credits, or membership benefits that reduce displayed fees

Do not proceed to checkout if cart has not reached the minimum order value for free delivery. If there are no more items in the running list, leave the items in the cart and do not place the order.

# Store Checkout

For each store, navigate to https://www.instacart.com/store/{store}/checkout_aisle. Example: https://www.instacart.com/store/morton-williams-supermarket/checkout_aisle

If user is brought to a `/{store}/checkout_aisle` page, click Continue to Checkout and proceed to the next step. Do not add additional items at this point.

If there is an active session, the delivery address should already be set. If set, do not change the delivery address.

In the Checkout page, in the section titled `When`, select the cheapest eligible delivery option. Prefer `Schedule and save`. If available, select `7pm-9pm` of the current day. If not available, select the next available `7pm-9pm` slot on a future date. If no `7pm-9pm` slot exists, choose the cheapest scheduled slot and state that clearly.

After changing the delivery window, verify that the selected slot actually stuck in the checkout summary before moving on.

Payment should be completed using a saved credit card. Since a saved credit card is available during checkout, there is no need to add or change payment methods.

If there is a minimum spend required to get free delivery, and the current cart total is below that threshold, add more items from the running list until the threshold is met. If there are no more items in the running list and the cart total is still below the threshold for free delivery, do not place the order.

Before placing the order, provide a concise structured approval summary in the current session or channel containing:
- store
- items added
- items not found
- substitutions chosen
- delivery window
- total
- whether free delivery was achieved

Ask for approval in the current session or channel before placing the order. If approval is given, place the order. If approval is not given, do not place the order.

Proceed to the next store and repeat the checkout process only for stores where the store cart exceeds the minimum order value.

# After Checkout Completion

- remove only items that were successfully ordered from the running list in the current channel
- keep not-found items in the running list
- keep declined substitutions out of completed items
- report the final order confirmation details back in the current session or channel
- Immediately set up a "order status check" cron task to check Instacart order status on https://www.instacart.com/store/orders/{orderId} every 10 minutes. 
- During each order status check run, if there is an order status change, do not send any message to channel and do not notify user. 
- If order status indicates order is delivered, send message to notify user of order arrival. 
- If order status indicated order is delayed, send message to notify user of order delay.  
- After Instacart order is delivered, archive or delete "order status check" cron task 
