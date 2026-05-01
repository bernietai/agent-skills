---
name: auto-order-instacart
description: From a running list of items, automatically place an order on Instacart for pickup or delivery.
metadata: { "openclaw": { "requires": { "bins": ["curl"] } } }
---
# Active session required

To place an order on Instacart, the agent must have access to an active session in the browser. Using curl, the agent should use the same "Cookie" header from the active session to make GraphQL requests to add items to the cart and complete the checkout process.

# Adding items to a Store Cart

From a RUNNING LIST of items in the current channel, manage multiple carts for different stores on Instacart. From an agent's memory, determine the preferred BRAND and STORE before adding the item to the cart. Search for a store using URL format: https://www.instacart.com/store/s?k={store_name}. If the store is found, select that store. 

Do not select stores that are more than 10 miles from the delivery address. 

Search the item using URL format: https://www.instacart.com/store/{store}/s?k={item}. If the BRAND is known, include brand using URL format: https://www.instacart.com/store/{store}/s?k={brand}+{item}. 

If the item is found, add the required {quantity} of the {item} to cart. If no quantity is available, default to 1 unit. 

If item is not found, and no acceptable alternative is found, keep item in the running list and process the next item in the list. 

# Initiate Store Checkout after reaching minimum order value

On instacart.com, each Store has a minimum order value required for free delivery. 

Do not proceed to checkout if cart has not reached minimum order value for free delivery. If there are no more items in the running list, leave the items in the cart and do not place the order. 

# Store Checkout 

For each store, navigate to https://www.instacart.com/store/{store}/checkout_aisle. eg https://www.instacart.com/store/morton-williams-supermarket/checkout_aisle

If user is brought to a "/{store}/checkout_aisle" page, click Continue to Checkout button to proceed to the next step. Do not add any additional items to the cart at this point.

If there is an active session, the delivery address should already be set. If set, do not change delivery address. 

In the Checkout page, in a section titled "When", select "Schedule and Save" to ensure the cheapest available delivery option is used. If available, select 7pm-9pm of the current day. If not available, select the next available 7pm-9pm slot in a future date. 

Payment should be completed using a saved credit card. Since a saved credit card is available during checkout, there is no need to add or change payment methods. 

If there is a minimum spend required to get free delivery, and the current cart total is below that threshold, add more items from the running list to the cart until the threshold is met. If there are no more items in the running list, and the cart total is still below the threshold for free delivery, do not place the order.

Ask for approval in current session or channel before placing the order. If approval is given, place the order. If approval is not given, do not place the order. 

Proceed to the next store and repeat the checkout process only for Stores where the Store Cart exceeds minimum order value. 

# After Checkout Completion

After order is complete, remove items that were successfully ordered from the running list in the current channel. 