# Stripe for Mantle
A Stripe component closely integrated with Mantle and Moqui and has no dependency on the Stripe SDK.

## What you need to know:
Uses [Stripe Checkout](https://stripe.com/payments/checkout) to gather payment information and the payment from the user.

To work properly: Requires [mantle-usl](https://github.com/moqui/mantle-usl) commit [`8503eed`](https://github.com/moqui/mantle-usl/commit/8503eeda6bfb7ee8c3207a4fda61487fc42895a7) or later

### Usage
```Note: Can be used with a party logged in or from a guest checkout perspective properly.```
Create a SalesOrder with a Product with a valid ProductStore and ProductPrice
call stripe.StripeServices.create#CheckoutFromSalesOrder with the order, and **your** successUrl and cancelUrl
```Optionally set the setup_future_usage to "off_session" to allow for charging customer on their payment method (like in a subscription or usage based model)```
Redirect the user to the `checkoutUrl` returned from the service (note the checkoutUrl and checkoutId are added to the OrderPart that is passed in)
If this is the first time the user has checkout out (or they're in not logged in), they will enter their payment information like name, credit card(depending on payment methods configurd in stripe), and billing address.
If the user is already logged in or the `orderPart.customerPartyId` is set and that party has a valid PartyIdentification with type `PtidSci` and a valid payment method in stripe, the user will only need to type in new information if they want to change it.
The user will then submit the payment in the Stripe Checkout screen and will redirect to your `successUrl`.
You will then need to deliver the Product / Service to the User who just paid (this can be done through checking the status of the `OrderPart` and if the `OrderPart` is in the `OrderPlaced` or later status then it should be okay to deliver the product to the `OrderPart.customerPartyId` )

At any time, if the customer wants to cancel the order part, call `stripe.StripeServices.cancel#CheckoutFromSalesOrderPart`
If the User is in the Stripe Checkout screen, Stripe should redirect to your customer to `cancelUrl` where they can restart their Order, leave feedback, or whatever

### **You** are responsible for: 
- Setting up the stripe account with your business profile and tax information
- Receive payouts from Stripe
- Inserting publishable key, secret key, and shared secret from the stripe dashboard to your SystemMessageRemote see: https://github.com/coarchy/stripe/blob/bf9d4610c0c7d560d4c3df6561a7d30e669ed341/data/StripeDemoData.xml#L18
- Create a PaymentGatewayConfig calling the stripe services see: https://github.com/coarchy/stripe/blob/bf9d4610c0c7d560d4c3df6561a7d30e669ed341/data/StripeDemoData.xml#L25C28-L25C48
- Create a ProductStore attached to the PaymentGatewayConfig with appropriate settings for approving Orders see: https://github.com/coarchy/stripe/blob/bf9d4610c0c7d560d4c3df6561a7d30e669ed341/data/StripeDemoData.xml#L29
- Create products that are part of the ProductStore with Prices
- Code for creating a checkout and redirecting the user to the Stripe Checkout page see: https://github.com/coarchy/stripe/blob/bf9d4610c0c7d560d4c3df6561a7d30e669ed341/service/stripe/StripeServices.xml#L31
- Code for handling the success or failure of the checkout session see: https://github.com/coarchy/stripe/blob/bf9d4610c0c7d560d4c3df6561a7d30e669ed341/service/stripe/StripeServices.xml#L26
- Delivering your product based on whether the checkout was successful

### Things to consider doing:
- Configure the Stripe Checkout branding settings: [production](https://dashboard.stripe.com/settings/branding) [test](https://dashboard.stripe.com/test/settings/branding)
- Configure the Handling of the stripe webhooks. Do this by:
  - Creating a seca for the service before or after the stripe service potential services:
    - Webhook checkout.session.completed: stripe.StripeServices.consume#CheckoutSessionCompleted
    - Webhook checkout.session.expired: stripe.StripeServices.consume#CheckoutSessionExpired
    - Service: stripe.StripeServices.create#CheckoutFromSalesOrder
    - Service: stripe.StripeServices.cancel#CheckoutFromSalesOrderPart
  - Overriding the services by creating a file in compone://yourcomponent/service/stripe/StripeServices.xml with the same name as the service overriding

### Things not considered:
- Moqui Refunds (message me here for implementation: https://forum.moqui.org/u/michael/summary)
- Moqui Vendor Rejection seca (message me here for implementation: https://forum.moqui.org/u/michael/summary)
- Stripe Taxes
- Stripe Subscriptions
- Stripe Customer Portal
- Stripe Connect
- Stripe Invoices
- Stripe Invoice Emails
- Stripe Receipt Emails

### Limitations
- stripe is a big set of products, but we only use a very tiny subset of them here
- at the moment no vaulting functionality for a customer service agent entering information on behalf of a customer
