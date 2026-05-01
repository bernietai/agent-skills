---
name: auto-order-wholefoods
description: From a running list of items, automatically place an order on Wholefoodsmarket.com for pickup or delivery.
metadata: { "openclaw": { "requires": { "bins": ["openclaw", "curl"] } } }
---
# Active session required

To place an order on Wholefoodsmarket.com, the agent must have access to an active session in the browser. 

# Adding items to a Store Cart

From a RUNNING LIST of items in the current channel, deteremine the preferred store for each item from the Agent's memory. Where the preferred store is Whole Foods, search for the item on Wholefoodsmarket.com using URL format: https://www.wholefoodsmarket.com/grocery/search?k={item}. 

If the item is found, add the required {quantity} of the {item} to cart. If no quantity is available, default to 1 unit. 

If item is not found, and no acceptable alternative is found, keep item in the running list and process the next item in the list. 

# Initiate Checkout after reaching minimum order value

Do not proceed to checkout if cart has not reached minimum order value for free delivery. If minimum order value is not reached, and there are no more items in the running list, leave the items in the cart and do not place the order. 

# Store Checkout 

To checkout, navigate to https://www.wholefoodsmarket.com/grocery/cart, then click on "Checkout On Amazon"

If user is brought to a "/alm/byg/" page, click "Continue" button to proceed to the next step. Do not add any additional items to the cart at this point.

If user is brought to a "/alm/substitution/" page, click "Continue" button to proceed". 

If there is an active session, the delivery address should already be set. If set, do not change delivery address.

If there is a minimum spend required to get free delivery, and the current cart total is below that threshold, add more items from the running list to the cart until the threshold is met. If there are no more items in the running list, and the cart total is still below the threshold for free delivery, do not place the order.

Ask for approval in current session or channel before placing the order. If approval is given, place the order. If approval is not given, do not place the order. 

Proceed to the next store and repeat the checkout process only for Stores where the Store Cart exceeds minimum order value. 

# After Checkout Completion

After order is complete, remove items that were successfully ordered from the running list in the current channel. 