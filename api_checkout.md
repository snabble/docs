
# Products Management API

This documentation describes the snabble API endpoints related to the management and simple access of products.
These endpoints are available on the `api` subdomain. See [General API access](api_general.md) for general
information about api access.

### Info operation

* [Create checkout info](#create-checkout-info): `POST /{project}/checkout/info`

### Process operations

* [List checkout processes](#list-checkout-processes): `GET /{project}/checkout/process`
* [Create checkout process](#create-checkout-process): `POST /{project}/checkout/process`
* [Get checkout process](#get-checkout-process): `GET /{project}/checkout/process/{id}`
* [Abort checkout process](#abort-checkout-process): `PATCH /{project}/checkout/process/{id}`
* [Create checkout process appoval](#create-checkout-process-approval): `POST /{project}/checkout/process/{id}/approvals`

### Payment operations

* [Create payment](#create-payment): `POST /{project}/checkout/payment`
* [Get payment](#get-payment): `GET /{project}/checkout/payment/{id}`
* [Set payment state](#set-payment-state): `PATCH /{project}/checkout/payment/{id}/state`


### Content Types

**Content-Type** : application/json

The payload is a JSON document of the specified type.

## Data Model

### Cart

A shoping cart request within a request from a client.

Example:
```
{
  "session": "d06474fa-1584-11e8-b642-0ed5f89f718b",
  "shopID": "shop-01",
  "items": [
    {"sku": "1", "amount": 2},
    {"sku": "2", "amount": 1},
    {"sku": "3", "amount": 42}
  ]
}
```

### Signed Checkout Info
A signed checkout info document with mandatory price calculation and available payment methods.

Example:

```
{
   "checkoutInfo" : {
      "price" : {
         "tax" : {
            "19" : 7673
         },
         "netPrice" : 40382,
         "price" : 48055
      },
      "availableMethods" : [
         "cash",
         "girocard",
         "sepa",
         "visa",
         "mastercard"
      ],
      "shopID" : "shop-01",
      "lineItems" : [
         {
            "totalPrice" : 798,
            "amount" : 2,
            "name" : "Kugelschreiber tarent rot",
            "price" : 399,
            "taxRate" : 19,
            "sku" : "1"
         },
         {
            "price" : 1099,
            "name" : "tarent logo-Cap grau",
            "totalPrice" : 1099,
            "amount" : 1,
            "taxRate" : 19,
            "sku" : "2"
         },
         {
            "amount" : 42,
            "totalPrice" : 46158,
            "name" : "tarent logo-Cap rot",
            "price" : 1099,
            "sku" : "3",
            "taxRate" : 19
         }
      ],
      "project" : "demo",
      "session" : "d06474fa-1584-11e8-b642-0ed5f89f718b"
   },
   "signature" : "Dynit3mnsBnM8eA9/zpkewRiibLcIFxOXSza8frxEKPvjsJo1PJGC7NeCKpGlHKG4SYPSEFeeF7cUKJeUKj9hntHzlIeDBrNPVQJOvH93CGsTuujAr6USCRYBaEnax0zNlCgEb0MhgJskXmoi7tHBNHl5C6vWZFviFGIFyVxPnt39vu3AE5SN9Qf9z16WqgcdGQUVMjy83geMq/AnhIsixr8SNcwWtIJu/+w1cfweR3KwCePMJnTzb9DHZY6X+ZSq6NRojtupyYiGG60shCiEXQpJ9s4RVWBXGQc3Sda6UZ5+IiwNPftTPNYmLNF6bSHpZ/2dz3QLpXwEoH6e1ThZtcixYpHu4AmKiIr3/GdsNvjCr2BdACfApDMYLRHuu5fWi/7sviz36Aw43i/JmFhZcjxqUz807tjdaVskiA7PGhYx54DXzE6kiQAYIJVgrwfvQIJY+ajDz4bVIon61g0KLKX58KtOrHUUL9U8BNc/PwcjrPxjjU2/nBnC/ujy8XSl3JWvxGni+StNuCSkPd9SKMsIDA4+03zm3JVqzM+JJApPl7vFryIxRQH3oEWDavI10O4x0nLT139+fpijWPml1yLGtPVylw0WeX7JlPJCSivAXAJkQWPFWSeYBqEvBu6jiEJmQob8Dd126dQYEybZBdWQ8SIY2f+zo/A5OQea0I"
}
```

### Create Checkout Process Request

Example:
```
{
    "signedCheckoutInfo" : { .. signedCheckoutInfo .. },
    "paymentMethod": "cash"
}
```

### Checkout Process
Example:
```
{
   "supervisorApproval" : null,
   "paymentApproval" : null,
   "aborted" : false,
   "checkoutInfo" : { .. checkoutInfo .. },
   "paymentMethod" : "cash",
   "modified" : false
}
```

### Checkout Process List
Example:
```
{
   "processes" : [
        {
           "supervisorApproval" : null,
           "paymentApproval" : null,
           "aborted" : false,
           "checkoutInfo" : { .. checkoutInfo .. },
           "paymentMethod" : "cash",
           "modified" : false
        },
        {
           "supervisorApproval" : null,
           "paymentApproval" : null,
           "aborted" : false,
           "checkoutInfo" : { .. checkoutInfo .. },
           "paymentMethod" : "cash",
           "modified" : false
        }
    ]
}
```

### Checkout Approval

Possible Types:
* supervisor

Example:
```
{"type": "supervisor"}
```


-----------

## Create checkout info
`POST /{project}/checkout/info`

Create a singed checkout info document with mandatory price calculation and available payment methods.
This document can be used to show the real price to the user and it can be used to start a checkout process
as input of [Create checkout process](#create-checkout-process).

**Required permissions** : productsRead

### Request
**Content-Type** : application/json

**Data** : [Cart](#cart).

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Signed Checkout Info](#signed-checkout-info).


-----------

## Create checkout process
`POST /{project}/checkout/process`

Initiate a checkout process.

**Required permissions** : productsRead and a singed checkout info

### Request
**Content-Type** : application/json

**Data** : [Create Checkout Process Request](#create-checkout-process-request).

### Success Response `201 Created`

**Location** : [Get checkout process](#get-checkout-process)

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## Get checkout process:
`GET /{project}/checkout/process/{id}`

Get the state of a checkout process.

**Required permissions** : productsRead and the id of a checkout process

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## List checkout processes
`GET /{project}/checkout/process?shop=shopID`

List all available checkout processes for a shop.

**Parameter** : shop (string) - the shop id

**Required permissions** : supervisor

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process List](#checkout-process-list)

-----------

## Abort checkout process
`PATCH /{project}/checkout/process/{id}`

Abort a checkout process.

**Required permissions** : productsRead and the id of a checkout process

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## Create checkout process appoval
`POST /{project}/checkout/process/{id}/approvals`

Create an approval for a checkout process.

### Request
**Content-Type** : application/json

**Data** : [Create Checkout Approval Request](#create-checkout-approval-request).

### Success Response `201 Created`

**Content-Type** : application/json

**Data** : [Checkout Approval](#checkout-approval).

-----------

## Create payment
`POST /{project}/checkout/payment`

Create a payment for a checkout process.

### Request

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

## Get payment
`GET /{project}/checkout/payment/{id}`

## Set payment state
`PATCH /{project}/checkout/payment/{id}/state`

