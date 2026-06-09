---
name: ticket-scan-tdf
description: From a running list of shows the user has indicated interest in, scan for available dates and ticket prices on tdf.org and notify user in channel. 
metadata:
  openclaw:
    requires:
      bins:
        - openclaw
        - curl
        - op 
---
### Prerequisites

- TDF Membership - an active membership is required to access ticket availability and pricing information. If a 1Password item titled "tdf.org" is available, use the configured email and password in 1Password item to log into tdf.org. If user already has an active seession on tdf.org in the browser, the email of the logged in profile must match the 1Password item titled "tdf.org". If the above conditions are not met, do not proceed with login. 
- Active credit card - a valid credit card must be stored in 1Password for checkout. If no credit card is available in 1Password, report back to user and keep all items in the running list for future processing. Do not proceed with checkout.

### Allowed URLs
- tdf.org
- windcave.com 

All browsing and transactions must occur within these domains. Do not navigate to or interact with any other domains.

### Execution mode

Prefer a browser-first workflow when the live session is stable enough to search, inspect results, verify cart contents, and complete checkout.

## Adding items to cart

Before adding items to cart, clear all old items from cart 

Search for items in the running list of shows. If matching item is found, add the required {quantity} of the {item} to cart. If no quantity is available, default to 1 unit.

### Item matching and request normalization 

When multiple plausible results exist, rank them using this order:

- exact show title match
- reasonable venue match

### Substitution Rules

If the exact item is not found, do not attempt to find a substitute. Keep the item in the running list and continue to the next item.

### Required add verification 

After each attempt to add to cart, verify success using at least one real confirmation signal such as:

- cart item count changed as expected
- the item appears in cart / checkout summary
- quantity for that item reflects the intended amount
- Do not assume an add succeeded just because an Add button was clicked.

If item is not found, and no acceptable alternative is found, keep item in the running list and process the next item in the list.

After adding the first item cart, a 10 minute countdown is initiated. Remind the user to confirm checkout within that time. If user confirms, proceed to checkout. If user does not confirm within 10 minutes, or explicitly declines to checkout, do not proceed and keep all items in the running list for future processing.

## Discount code or Gift card 

If user indicates a discount code is available, in "/cart/details" page, enter the code in the input box labelled "Promo Code" the click "submit promo". If the code is invalid, report back to user and proceed with order. 

## Initiate Store Checkout after confirmation 

- From "/cart/details" page, click "Check Out" button to proceed to Review Order and Purchase page with url "/cart/payment". 
- Click "Enter Payment Information" button to proceed to a payment page hosted on sec.windcave.com domain. 

## Payment page 

- If no credit card available, report back to user and keep all items in the running list for future processing. Do not proceed with checkout. 
- If credit card available, enter the following information on the payment page: 
  - Card number
  - Name On Card
  - Expiry date
  - CVC code
  - Captcha tickbox labelled "I'm not a robot"
  - Click "Submit" button to submit the order.

## After Checkout Completion

After the order is complete:

- remove only items that were successfully ordered from the running list in the current channel
- keep not-found items in the running list
- keep declined substitutions out of completed items
- report the final order confirmation details back in the current session or channel
