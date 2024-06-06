# Stripe for Mantle

## High Level Overview

| Process Story Activity                                                                                                                                                                                                       | Responsiblity      |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| During stripe component setup, the developer sets up a [stripe api publishable and secret key](https://docs.stripe.com/keys#:~:text=To%20create%20a%20secret%20API,or%20in%20a%20text%20message) for demo or production use. | `You` 
| Create an order in open status with a customer that is set to the currently logged in user                                                                                                                                   | `Your Application` |
| Redirect user to the `/stripe/Checkout` screen with the orderId, done redirect url, and a error redirect url.                                                                                                                | `Your Application` |
|                                                         <br/>                                                                                                                                                                     | `Stripe`           |
|                                                                                                                                                                                                                              |                    |

Make sure initial integration is working across service implemented in Moqui by default
- authorizeAndCapture
- separate authorize and capture
-  authorize, capture, release, refund, get#PaymentGatewayDetails
Refactor checkout to use orders instead of payments

Have example screen for how to integrate into a saas application

Notice for fraud checking JS

Better Documentation for how to use it in the first place
- include links to stripe configuration of the stripe ui
- include links to the stripe api
- talk about PaymentGatewayResponse

Limitation of it
- stripe is a big set of products, but we only use a very tiny subset of them here
- at the moment no vaulting functionality for a customer service agent entering information on behalf of a customer


