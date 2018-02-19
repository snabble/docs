
# Products Management API

This documentation describes the snabble API endpoints related to the management and simple access of products.
These endpoints are available on the `api` subdomain. See [General API access](api_general.md) for general
information about api access.

### Info operation

* [Create Checkout Info](#create-checkout-info): `POST /{project}/checkout/info`

### Process operations

* [List Checkout Processes](#list-checkout-processes): `GET /{project}/checkout/process`
* [Create Checkout Process](#create-checkout-process): `POST /{project}/checkout/process`
* [Get Checkout Process](#get-checkout-process): `GET /{project}/checkout/process/{id}`
* [Modify Checkout Process](#modify-checkout-process): `PATCH /{project}/checkout/process/{id}`
* [Create Checkout Process Appoval](#create-checkout-process-approval): `POST /{project}/checkout/process/{id}/approval`

### Payment operations

* [Create payment](#create-payment): `POST /{project}/checkout/payment`
* [Get payment](#get-payment): `GET /{project}/checkout/payment/{id}`
* [Set payment state](#set-payment-state): `PATCH /{project}/checkout/payment/{id}/state`

### Data Model

* [Cart](#cart)
* [Signed Checkout Info](#signed-checkout-info)
* [Create Checkout Process Request](#create-checkout-process-request)
* [Checkout Process](#checkout-process)
* [Checkout Process List](#checkout-process-list)
* [Checkout Approval](#checkout-approval)
* [Abort Request](#abort-request)

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
A signed Checkout Info document with mandatory price calculation and available payment methods.

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
   "signature" : "Dynit3mnsBnM8eA9.."
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

### Abort Request
Example:
```
{"aborted": true}
```


-----------

## Create Checkout Info
`POST /{project}/checkout/info`

Create a singed Checkout Info document with mandatory price calculation and available payment methods.
This document can be used to show the real price to the user and it can be used to start a Checkout Process
as input of [Create Checkout Process](#create-checkout-process).

**Required permissions** : productsRead

### Request
**Content-Type** : application/json

**Data** : [Cart](#cart).

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Signed Checkout Info](#signed-checkout-info).


-----------

## Create Checkout Process
`POST /{project}/checkout/process`

Initiate a Checkout Process.

**Required permissions** : productsRead and a singed Checkout Info

### Request
**Content-Type** : application/json

**Data** : [Create Checkout Process Request](#create-checkout-process-request).

### Success Response `201 Created`

**Location** : [Get Checkout Process](#get-checkout-process)

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## Get Checkout Process:
`GET /{project}/checkout/process/{id}`

Get the state of a Checkout Process.

**Required permissions** : productsRead and the id of a Checkout Process

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## List Checkout Processes
`GET /{project}/checkout/process?shop=shopID`

List all available Checkout Processes for a shop.

**Parameter** : shop (string) - the shop id

**Required permissions** : supervisor

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process List](#checkout-process-list)

-----------

## Modify Checkout Process
`PATCH /{project}/checkout/process/{id}`

Modify a Checkout Process.

**Required permissions** : productsRead and the id of a Checkout Process

### Abort Request
Mark a checkout process as aborted.

**Content-Type** : application/json

**Data** : [Abort Request](#abort-request).

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## Create Checkout Process Approval
`POST /{project}/checkout/process/{id}/approval`

Create an approval for a Checkout Process.

### Request
**Content-Type** : application/json

**Data** : [Create Checkout Approval Request](#create-checkout-approval-request).

### Success Response `201 Created`

**Content-Type** : application/json

**Data** : [Checkout Approval](#checkout-approval).

-----------

## Create payment
`POST /{project}/checkout/payment`

Create a payment for a Checkout Process.

### Request

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

## Get payment
`GET /{project}/checkout/payment/{id}`

## Set payment state
`PATCH /{project}/checkout/payment/{id}/state`

