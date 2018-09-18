# Checkout Management API

This documentation describes the snabble API endpoints related to the management and simple access of checkouts.
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

### Data Model

* [Cart](#cart)
* [Checkout Info](#checkout-info)
* [Create Checkout Process Request](#create-checkout-process-request)
* [Checkout Process](#checkout-process)
* [Payment State](#payment-state)
* [Checkout Process List](#checkout-process-list)
* [Checkout Approval](#checkout-approval)
* [Abort Request](#abort-request)
* [Close Request](#close-request)
* [Update Pricing Request](#update-pricing-request)
* [Set Payment State Request](#set-payment-state-request)

### Content Types

**Content-Type** : application/json

The payload is a JSON document of the specified type.

## Data Model

### Cart

A shopping cart within a request from a client.

| Parameter     | Type     | Default | Description                                                          |
|---------------|----------|---------|----------------------------------------------------------------------|
| `sku`         | `string` |         | SKU of the product                                                   |
| `amount`      | `int`    |         | Number of products                                                   |
| `weight`      | `int`    | 0       | Weight of product in case of of a weighable (a not packaged) product |
| `units`       | `int`    | 0       | Number of units in a package in case of bundle or piece product      |
| `price`       | `int`    | 0       | Price of the product in case of an encoded price                     |
| `scannedCode` | `string` |         | Scanned code                                                         |

The properties `weight`, `units` and `price` are only considered if
the referenced products is of the correct type.

Example:

```
{
  "session": "d06474fa-1584-11e8-b642-0ed5f89f718b",
  "shopID": "shop-01",
  "items": [
    {"sku": "1", "amount": 2, "scannedCode": "0000000000001" },
    {"sku": "2", "amount": 1, "weight": 102, "scannedCode": "0000000000002"},
    {"sku": "3", "amount": 42, "scannedCode": "0000000000003"}
    {"sku": "4", "amount": 1, "units": 6, "scannedCode": "000000000004"},
    {"sku": "5", "amount": 1, "price": 129, "scannedCode": "0000000000005"},
  ],
  "customer": {
    "loyaltyCard": "..."
  }
}
```

### Checkout Info

A signed document with mandatory price calculation and available payment methods.

### Signed Checkout Info

| Parameter      | Type           | Default | Description                            |
|----------------|----------------|---------|----------------------------------------|
| `checkoutInfo` | `CheckoutInfo` |         | Actual Checkout Info                   |
| `signature`    | `string`       |         | Signature of the `checkoutInfo` object |

### Checkout Info

| Parameter          | Type              | Default | Description                                                    |
|--------------------|-------------------|---------|----------------------------------------------------------------|
| `session`          | `string`          |         | Identifier of the session                                      |
| `clientID`         | `string`          |         | The app id                                                                                    |
| `shopID`           | `string`          |         | Identifier of the shop                                         |
| `shop`             | `ShopInformation` |         | Shop details                                                   |
| `customer`         | `Customer`        |         | Customer details                                               |
| `createdAt`        | `date`            |         | Creation date of the checkout info (RFC3339 Datetime formated) |
| `availableMethods` | `string[]`        |         | List of payment methods available for this checkout            |
| `lineItems`        | `LineItem[]`      |         | List of the line items                                         |
| `price`            | `Price`           |         | Price of the checkout                                          |

### Shop Information

The Shop Information captures the details of the essential for the checkout.

| Parameter    | Type     | Default | Description                                |
|--------------|----------|---------|--------------------------------------------|
| `externalId` | `string` |         | An identifier provided by external sources |
| `name`       | `string` |         | The name of the shop                       |
| `email`      | `string` |         | Email address                              |
| `phone`      | `string` |         | Phone number                               |
| `street`     | `string` |         | Street and number                          |
| `zip`        | `string` |         | Postal code                                |
| `city`       | `string` |         | City                                       |
| `state`      | `string` |         | State                                      |
| `country`    | `string` |         | Country                                    |

#### Line Item

| Parameter         | Type     | Default | Description                                                                                                |
|-------------------|----------|---------|------------------------------------------------------------------------------------------------------------|
| `sku`             | `string` |         | SKU of the product                                                                                         |
| `amount`          | `int`    |         | Number of products / packages                                                                              |
| `weight`          | `int`    | 0       | Weight of product in case of of a weighable (a not packaged) product                                       |
| `units`           | `int`    | 0       | Number of units in a package in case of bundle or piece product                                            |
| `price`           | `int`    |         | Price of the product in case of an encoded price                                                           |
| `priceOrigin`     | `string` |         | Origin of the price (`master` price from the products list, `client` the price was provided by the client) |
| `name`            | `string` |         | Name of the product                                                                                        |
| `taxRate`         | `string` |         | Tax rate as string encoded decimal                                                                         |
| `scannedCode`     | `string` |         | Scanned code                                                                                               |
| `saleRestriction` | `string` |         | Restriction for product e.g. min age                                                                       |

Example:

```
{
    "links": {
      "checkoutProcess": {
        "href": "/demo/checkout/process"
      }
    },
    "checkoutInfo" : {
      "price" : {
         "tax" : {
            "19" : 7673
         },
         "taxNet" : {
            "19" : 40382
         },
         "taxPre" : {
           "19" : 48055
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
            "taxRate" : "19",
            "sku" : "1",
            "scannedCode": "0000000000001",
            "saleRestriction": "min_age_18"
         },
         {
            "price" : 1099,
            "name" : "tarent logo-Cap grau",
            "totalPrice" : 1099,
            "amount" : 1,
            "taxRate" : "19",
            "sku" : "2",
            "scannedCode": "0000000000002",
            "saleRestriction": ""
         },
         {
            "amount" : 42,
            "totalPrice" : 46158,
            "name" : "tarent logo-Cap rot",
            "price" : 1099,
            "sku" : "3",
            "taxRate" : "19",
            "scannedCode": "0000000000003"
         }
      ],
      "project" : "demo",
      "session" : "d06474fa-1584-11e8-b642-0ed5f89f718b",
      "clientID": "app-id-12345",
      "createdAt": "2018-06-19T09:59:29.245Z",
      "customer": {
        "loyaltyCard": "..."
      }
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

Process attributes:

| Parameter          | Type         | Default | Description                                                                                        |
|--------------------|--------------|---------|----------------------------------------------------------------------------------------------------|
| supervisorApproval | bool/nil     | nil     | Approval by the checkout supervisor (nil=pending, true=granted, false=rejected)                    |
| paymentApproval    | bool/nil     | nil     | Approval by the payment process (nil=pending, true=granted, false=rejected)                        |
| aborted            | bool         | false   | Flag, if the process was aborted by the user                                                       |
| closed             | bool         | false   | Flag, if the process was closed by the user. This flag is optional.                                |
| checkoutInfo       | checkoutInfo |         | The full [Checkout Info](#checkout-info) object (that was provided in the creation of the process) |
| pricing            | princing     |         | The [Pricing information](#pricing) of the checkout                                                |
| paymentMethod      | string       |         | A valid payment method                                                                             |
| paymentState       | string       | pending | The [Status of the associated payment process](#payment-state)                                     |
| paymentInformation | object       | nil     | Payment dependent additional information, i.e. a code to present to the user                       |
| modified           | bool         | false   | Flag, if the process was modified by the checkout supervisor                                       |
| createdAt          | date         |         | Creation date of the process (RFC3339 Datetime formated )                                          |

Example:
```
{
   "links": {
      "self": {"href": "/demo/checkout/process/f42a5718-1621-11e8-b642-0ed5f89f718b"},
      "approval": {"href": "/demo/checkout/process/f42a5718-1621-11e8-b642-0ed5f89f718b/approval"},
      "payment": {"href": "/demo/checkout/payment"}
   },
   "supervisorApproval" : null,
   "paymentApproval" : null,
   "aborted" : false,
   "checkoutInfo" : { .. checkoutInfo .. },
   "paymentMethod" : "cash",
   "pricing": { .. pricing .. },
   "modified" : false
}
```

### Payment State

Represents the state of the payment process associated with the Checkout Process.

Possible values are:

* `pending` (initial state): The payment is pending
* `processing`: A payment system is currently processing the process
* `transferred` (terminal state): The process was transferred to a
  payment system. But the outcome of the processing will not be
  communicated to the process.
* `successful` (terminal state)
* `failed` (terminal state)

### Checkout Process List
Example:
```
{
   "processes" : [
        {
           "links": {
              "self": {"href": "/demo/checkout/process/f42a5718-1621-11e8-b642-0ed5f89f718b"},
              "approval": {"href": "/demo/checkout/process/f42a5718-1621-11e8-b642-0ed5f89f718b/approval"},
              "payment": {"href": "/demo/checkout/payment"}
           },
           "supervisorApproval" : null,
           "paymentApproval" : null,
           "aborted" : false,
           "checkoutInfo" : { .. checkoutInfo .. },
           "paymentMethod" : "cash",
           "paymentState": "pending",
           "pricing": { .. pricing .. },
           "modified" : false
        },
        {
           "links": {
              "self": {"href": "/demo/checkout/process/f42a5718-1621-11e8-b642-xxd5f89f718b"},
              "approval": {"href": "/demo/checkout/process/f42a5718-1621-11e8-b642-xxd5f89f718b/approval"},
              "payment": {"href": "/demo/checkout/payment"}
           },
           "supervisorApproval" : null,
           "paymentApproval" : null,
           "aborted" : false,
           "checkoutInfo" : { .. checkoutInfo .. },
           "paymentMethod" : "cash",
           "paymentState": "pending",
           "pricing": { .. pricing .. },
           "modified" : false
        }
    ]
}
```

### Pricing

Price informations.

```
{
   "price" : {
      "tax" : {
         "19" : 7673
      },
      "taxNet" : {
        "19" : 40382
      },
      "taxPre" : {
        "19" : 48055
      },
      "netPrice" : 40382,
      "price" : 48055
   },
   "lineItems" : [
      {
         "totalPrice" : 798,
         "amount" : 2,
         "name" : "Kugelschreiber tarent rot",
         "price" : 399,
         "taxRate" : 19,
         "sku" : "1",
         "scannedCode": "0000000000001"
      },
      {
         "price" : 1099,
         "name" : "tarent logo-Cap grau",
         "totalPrice" : 1099,
         "amount" : 1,
         "taxRate" : 19,
         "sku" : "2",
         "scannedCode": "0000000000002"
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

### Close Request
Example:
```
{"closed": true}
```

### Update Pricing Request

Updates the pricing of the checkout process.

Example:
```
{
   "pricing" : {
      "lineItems" : [
         {
            "sku" : "1",
            "amount" : 1,
            "taxRate" : 19,
            "price" : 399,
            "totalPrice" : 399,
            "name" : "Kugelschreiber tarent rot"
         }
      ],
      "price" : {
         "price" : 399
      }
   }
}
```

### Set Payment State Request

Valid values are:

* `pending`
* `successful`
* `failed`

Example:
```
{
   "paymentState" : "successful"
}
```

-----------

## Create Checkout Info
`POST /{project}/checkout/info`

Creates a Checkout Info document with mandatory price calculation and available payment methods.
This document can be used to show the real price to the user and it can be used to start a Checkout Process
as input of [Create Checkout Process](#create-checkout-process).

**Required permissions** : productsRead

### Request
**Content-Type** : application/json

**Data** : [Cart](#cart).

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Info](#checkout-info).


-----------

## Create Checkout Process
`POST /{project}/checkout/process`

Initiate a Checkout Process.

**Required permissions** : productsRead and a Checkout Info

### Request
**Content-Type** : application/json

**Data** : [Create Checkout Process Request](#create-checkout-process-request).

### Success Response `201 Created`

**Location** : [Get Checkout Process](#get-checkout-process)

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## Get Checkout Process
`GET /{project}/checkout/process/{id}`

Get the state of a Checkout Process.

**Required permissions** : checkoutRead for the checkout process

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

### Aborting the Checkout Process

**Required permissions** : productsRead and the id of a Checkout Process

#### Abort Request
Mark a checkout process as aborted.

**Content-Type** : application/json

**Data** : [Abort Request](#abort-request).

#### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

### Closing the Checkout Process

**Required permissions** : productsRead and the id of a Checkout Process

#### Close Request
Mark a checkout process as closed.

**Content-Type** : application/json

**Data** : [Close Request](#close-request).

#### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

### Set Payment State of the Checkout Process

**Required permissions** : setPaymentState and the id of a Checkout Process

#### Set Payment State Request
Mark a checkout process as successful, failed or in process.

**Content-Type** : application/json

**Data** : [Set Payment State Request](#set-payment-state-request).

#### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

### Update the Pricing of the Checkout Process

**Required permissions** : updatePricingCheckoutProcess and the id of a Checkout Process

#### Update Pricing Request
Update the pricing of the CheckoutProcess.

The provided pricing might be incomplete. This means whole line items
or parts of them (like `taxRate`) might be missing. Also the
`netPrice` and the `tax` properties are optional. This leads to the
generation of incomplete orders. In this case downstream processes
like the generation of receipts might not be possible or can't
generate the correct results.

**Content-Type** : application/json

**Data** : [Update Pricing Request](#update-pricing-request).

#### Success Response `200 OK`

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
