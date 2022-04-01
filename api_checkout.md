> :warning: **Outdated document**: The snabble documentation has beeen moved to [docs.snabble.io](https://docs.snabble.io).
>
> :point_right: The current version of this document can befound at [docs.snabble.io/docs/api/api_checkout](https://docs.snabble.io/docs/api/api_checkout).


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

| Parameter     | Type         | Description                                                          |
|---------------|--------------|----------------------------------------------------------------------|
| `clientID`    | `string`     | The ID of the client/device                                          |
| `appUserID`   | `string`     | The ID of the appUser                                                |
| `session`     | `string`     | Current session ID                                                   |
| `shopID`      | `string`     | ID of the shop                                                       |
| `items`       | `CartItem[]` | Array of cart items                                                  |
| `customer`    | `object`     | Contains further information to the customer, e.g.loyaltyCard number |

A cart item contains following information:

| Parameter     | Type     | Default | Description                                                       |
|---------------|----------|---------|-------------------------------------------------------------------|
| `id`          | `string` | null    | ID of the item                                                    |
| `sku`         | `string` |         | SKU of the product                                                |
| `amount`      | `int`    |         | Number of products                                                |
| `weight`      | `int`    | null    | Weight of product in case of a weighable (a not packaged) product |
| `weightUnit`  | `string` | null    | Unit of the weight                                                |
| `units`       | `int`    | null    | Number of units in a package in case of bundle or piece product   |
| `price`       | `int`    | null    | Price of the product in case of an encoded price                  |
| `scannedCode` | `string` |         | Scanned code                                                      |

Example:

```
{
  "session": "d06474fa-1584-11e8-b642-0ed5f89f718b",
  "clientID":  "772fdd23-0161-4e3e-85d0-af2a52fbe3f7",
  "appUserID": "d7b5a85a-4186-466e-99ee-414fe93951f3",
  "shopID": "shop-01",
  "items": [
    {"sku": "1", "amount": 2, "scannedCode": "0000000000001" },
    {"sku": "2", "amount": 1, "weight": 102, "weightUnit": "g", "scannedCode": "0000000000002"},
    {"sku": "3", "amount": 42, "scannedCode": "0000000000003"},
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
| `clientID`         | `string`          |         | The ID of the client/device                                    |
| `appUserID`        | `string`                    | The ID of the appUser                                          |
| `shopID`           | `string`          |         | Identifier of the shop                                         |
| `shop`             | `ShopInformation` |         | Shop details                                                   |
| `customer`         | `Customer`        |         | Customer details                                               |
| `createdAt`        | `date`            |         | Creation date of the checkout info (RFC3339 Datetime formated) |
| `availableMethods` | `string[]`        |         | List of payment methods available for this checkout            |
| `lineItems`        | `LineItem[]`      |         | List of the line items                                         |
| `price`            | `Price`           |         | Price of the checkout                                          |
| `currency`         | `string`          |         | Currency of the project                                        |


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

A line item normaly represents a certain amount of a product. But it
can also used for additional items that are indivisble from the bought
product like a deposit. Further, it can be used to represent
promotions like a general discount. The line items are hence typed to
distinguish these cases. Also it can refer to another one through the
`refersTo` property.


| Parameter         | Type     | Default | Description                                                                                                                |
|-------------------|----------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `id`              | `string` |         | Identifier of the line item                                                                                                |
| `type`            | `string` | default | Type of the line item (`default`, `deposit`, `promotion`)                                                                  |
| `refersTo`        | `string` | null    | Line item that is related to this one (ie. if the line item represents a deposit the id of the line item wich requires it) |
| `sku`             | `string` |         | SKU of the product                                                                                                         |
| `amount`          | `int`    |         | Number of products / packages                                                                                              |
| `weight`          | `int`    | 0       | Weight purchased                                                                                                           |
| `weightUnit`      | `string` |         | Unit of the weight                                                                                                         |
| `referenceUnit`   | `string` |         | Reference unit for weighable item, Details: [Product API: weighable products](api_products.md#weighable-products)          |
| `units`           | `int`    | 0       | Number of units in a package in case of bundle or piece product                                                            |
| `price`           | `int`    |         | Price of the product for single unit, piece, weightUnit, etc                                                               |
| `priceOrigin`     | `string` |         | Origin of the price (`master` price from the products list, `client` the price was provided by the client)                 |
| `totalPrice`      | `int`    |         | Total price of the line item                                                                                               |
| `name`            | `string` |         | Name of the product                                                                                                        |
| `taxRate`         | `string` |         | Tax rate as string encoded decimal                                                                                         |
| `scannedCode`     | `string` |         | Scanned code                                                                                                               |
| `saleRestriction` | `string` |         | Restriction for product e.g. min age                                                                                       |

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
      "clientID":  "772fdd23-0161-4e3e-85d0-af2a52fbe3f7",
      "appUserID": "d7b5a85a-4186-466e-99ee-414fe93951f3",
      "shopID" : "shop-01",
      "shop": {
         "externalID": "shop-1234",
         "street": "Shop Street 3",
         "zip": "12345",
         "state": "Shop State",
         "country": "Some Country",
         "city": "Some City",
         "phone": "+4912342356",
         "email": "shop@someshop.de",
         "name": "Some Shop"
      },
      "lineItems" : [
         {
            "id": "e25bc1ee-5233-11e9-9e12-68f7286a148f",
            "type": "default",
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
            "id": "ecac5c4e-5233-11e9-b17e-68f7286a148f",
            "type": "default",
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
            "id": "f660bac8-5233-11e9-a689-68f7286a148f",
            "type": "default",
            "amount" : 42,
            "totalPrice" : 46158,
            "name" : "tarent logo-Cap rot",
            "price" : 1099,
            "sku" : "3",
            "taxRate" : "19",
            "scannedCode": "0000000000003"
         }
      ],
      "currency": "EUR",
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
    "paymentMethod": "sepa",
    "paymentInformation": { .. some information depending on payment system .. }
}
```

### Checkout Process

Process attributes:

| Parameter            | Type           | Default | Description                                                                                        |
|----------------------|----------------|---------|----------------------------------------------------------------------------------------------------|
| `supervisorApproval` | `bool/nil`     | nil     | Approval by the checkout supervisor (nil=pending, true=granted, false=rejected)                    |
| `paymentApproval`    | `bool/nil`     | nil     | Approval by the payment process (nil=pending, true=granted, false=rejected)                        |
| `aborted`            | `bool`         | false   | Flag, if the process was aborted by the user                                                       |
| `checkoutInfo`       | `CheckoutInfo` |         | The full [Checkout Info](#checkout-info) object (that was provided in the creation of the process) |
| `pricing`            | `pricing`      |         | The [Pricing information](#pricing) of the checkout                                                |
| `paymentMethod`      | `string`       |         | A valid payment method                                                                             |
| `paymentState`       | `string`       | pending | The [Status of the associated payment process](#payment-state)                                     |
| `paymentInformation` | `object`       | nil     | Payment dependent additional information, i.e. a code to present to the user                       |
| `paymentResult`      | `object`       | nil     | [Information from payment system](#payment-result), i.e. transaction id, state                     |
| `modified`           | `bool`         | false   | Flag, if the process was modified by the checkout supervisor                                       |
| `createdAt`          | `date`         |         | Creation date of the process (RFC3339 Datetime formated )                                          |
| `finalizedAt`        | `date`         |         | The time the process was finalized, i.e. the time it was paid                                      |
| `processedOffline`   | `bool`         | false   | Flag, if the customer was offline while creating the process                                       |
| `clientID`           | `string`       |         | The ID of the client/device                                                                        |
| `appUserID`          | `string`       |         | The ID of the appUser                                                                              |

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
   "modified" : false,
   "processedOffline": false,
   "clientID":  "772fdd23-0161-4e3e-85d0-af2a52fbe3f7",
   "appUserID": "d7b5a85a-4186-466e-99ee-414fe93951f3"
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

### Payment Result

The structure of the payment result depends on the payment method and
the provider used. In general three cases have to be considered:
Online Payment Credit Card, Online SEPA Direct Debit and Payment
Terminal. In this cases the system assures that at least the described
fields are present.

#### Payment Result: Payment Credit Card

The payment was processed online using a Credit Card, ie. Visa, Mastercard.

| Parameter                  | Type     | Description                                        |
|----------------------------|----------|----------------------------------------------------|
| `transactionTime`          | `string` | Transation time as unix timestamp in time zone UTC |
| `pan`                      | `string` | Annonymized Primary Account                        |
| `processorApprovalCode`    | `string` | Approval code of the transaction                   |
| `processorReferenceNumber` | `string` | Reference number of the transaction                |

#### Payment Result: SEPA Direct Debit

The payment was processed online using a Direct Debit transaction

| Parameter                  | Type     | Description                                        |
|----------------------------|----------|----------------------------------------------------|
| `transactionTime`          | `string` | Transation time as unix timestamp in time zone UTC |
| `mandateReference`         | `string` | SEPA Mandate reference                             |
| `processorApprovalCode`    | `string` | Approval code of the transaction                   |
| `processorReferenceNumber` | `string` | Reference number of the transaction                |

#### Payment Result: Payment Terminal

The payment was processed using a payment terminal with the ZVT-Protocol.

| Parameter     | Type     | Description                           |
|---------------|----------|---------------------------------------|
| `time`        | `string` | Transation time as encoded as RFC3339 |
| `aid`         | `string` | Authorization Code                    |
| `pan`         | `string` | Primary Account Number                |
| `paymentType` | `string` | ZVT-Payment Type                      |


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

Price information.

| Parameter          | Type           | Description                                                                                                 |
|--------------------|----------------|-------------------------------------------------------------------------------------------------------------|
| `tax`              | `Object`       | Map of tax rates (as string encoded decimals) to the portion of the price that was taxed with this rate     |
| `taxNet`           | `Object`       | Map of tax rates (as string encoded decimals) to the portion of the net price that was taxed with this rate |
| `taxPre`           | `Object`       | Map of tax rates (as string encoded decimals) sums all pre tax prices of products with this rate up         |
| `lineItems`        | `LineItem[]`   | Line items of the order. For details see [Checkout API: Line Item](api_checkout.md#line-item)               |


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
* `processing`
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

### Possible structured errors

More information on structured errors in [General API](api_general.md#structured-errors)

| Type                  | Description                                                                                                       |
|-----------------------|-------------------------------------------------------------------------------------------------------------------|
| `shop_not_found`      | [shop for provided shopID](api_shops.md) does not exists                                                          |
| `bad_shop_id`         | The provided `shopID` is either not present or invalid                                                            |
| `no_available_method` | No payment method is available                                                                                    |
| `invalid_cart_item`   | One or multiple cart items are invalid. The api returns an array of [lineItemErrors](#LineItemError) in `details` |


#### LineItemError

A `LineItemError` gives further information on line item which caused an error.

| Property   | Type        | Description                              |
|------------|-------------|------------------------------------------|
| `sku`      | `string`    | SKU of the affected product              |
| `type`     | `string`    | line item error types                    |
| `message`  | `string`    | error message                            |


Possible line item error types:

| Type                | Description                                    |
|---------------------|------------------------------------------------|
| `sale_stop`         | product is affected by a sale stop             |
| `product_not_found` | product with the given SKU does not exist      |


Example:
```
{
   "type": "invalid_cart_item",
   "details": [
      {
         "sku": "some-sku",
         "type": "sale_stop",
         "message": "product is excluded from sale"
      },
      {
         "sku": "some-other-sku",
         "type": "product_not_found",
         "message": "product not found"
      }
   ]
}
```



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
