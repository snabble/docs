# PoS Integration

This documentation describes the snabble API endpoints related to the
Point of Sale (PoS) integration.  These endpoints are available on the
`api` subdomain. See [General API access](api_general.md) for general
information about api access.

## Operations

* [Get checkout process](#get-checkout-process): `GET /{project}/pos/checkout/id/{id}`
* [Update checkout process](#put-checkout-process): `PUT /{project}/pos/checkout/id/{id}`
* [Partially update checkout process](#patch-checkout-process): `PATCH /{project}/pos/checkout/id/{id}`

### Data Model

* [Checkout Process](#checkout-process)

## Workflow

The integration of snabble into a Point of Sale software system (PoS)
is implemented through a handover of an identifier from the snabble
app to the PoS using a standard QR-Code. After the handover the PoS
can fetch and process the checkout. Finally, it updates it to reflect
the outcome of the process.

The QR-Code presented by the user contains the identifier of the
checkout process. It looks like this:
```
snabble:752dd716-ec0c-11e8-8528-68f7286a148f
```

The string after the colon is the identifier of the checkout process.

To build the URL of the process the base URL of the used snabble
environment (see [General API access:
Environments](api_general.md#environments)), the identifier of the
project and the identifier of the checkout are required. This is the URL
template:

```
<base URL>/<project id>/pos/checkout/id/<checkout id>
```
ie. `https://api.snabble.io/test-project-12345/pos/checkout/id/752dd716-ec0c-11e8-8528-68f7286a148f`.

After the PoS has accessed the identifier of the checkout process form
the QR-Code and has build the URL as above, it should perform a
request to update the state of the checkout process to
`processing`. This communicates that it started to handle the checkout
process. Additionally it might identify itself by sending an unique
identifier. The service responds with the complete updated checkout
process, which should be used by PoS to process the checkout. The call
to the service might look like:

```
PATCH /project/pos/checkout/id/752dd716-ec0c-11e8-8528-68f7286a148f HTTP/1.1
Host: api.snabble.io
Content-Type: application/merge-patch+json
Client-Token: ...

{
  "state": "processing",
  "processedBy": "pos-1"
}
```
The service responds with:
```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "lineItems" : [
    {
      "scannedCode": "0000000000001",
      "totalPrice" : 798,
      "amount" : 2,
      "price" : 399,
      "taxRate" : 19,
      "sku" : "1"
    },
    {
      "scannedCode": "0000000000002"
      "price" : 1099,
      "totalPrice" : 1099,
      "amount" : 1,
      "taxRate" : 19
    }
  ],
  "tax" : {
    "19" : 7673
  },
  "taxNet" : {
    "19" : 40382
  },
  "netPrice" : 40382,
  "price" : 48055,
  "state": "processing",
  "processedBy": "pos-1",
  "loyaltyCard": "12345678"
}
```

After successfully processing the checkout the PoS should update the
checkout process. It should update the line items and the price to
reflect the performed checkout. Further, it should set the state to
`successful`.

```
PATCH /project/pos/checkout/id/752dd716-ec0c-11e8-8528-68f7286a148f HTTP/1.1
Host: api.snabble.io
Content-Type: application/merge-patch+json
Client-Token: ...

{
  "lineItems" : [
    {
      "scannedCode": "0000000000001",
      "totalPrice" : 798,
      "amount" : 2,
      "price" : 399,
      "taxRate" : 19,
      "sku" : "1"
    },
    {
      "scannedCode": "0000000000002"
      "price" : 1099,
      "totalPrice" : 1099,
      "amount" : 1,
      "taxRate" : 19
    }
  ],
  "tax" : {
    "19" : 7673
  },
  "taxNet" : {
    "19" : 40382
  },
  "netPrice" : 40382,
  "price" : 48055,
  "state": "successful",
  "loyaltyCard": "12345678"
}
```

If the checkout could not be performed, the PoS should update the
checkout process and set its state to `failed`. Optionally the PoS can
provide an informal `failedReason` message, which describes the
conditions of the failure.

```
PATCH /project/pos/checkout/id/752dd716-ec0c-11e8-8528-68f7286a148f HTTP/1.1
Host: api.snabble.io
Content-Type: application/merge-patch+json
Client-Token: ...

{
  "state": "failed",
  "failedReason": "Payment failed"
}
```

## Data Model

### Checkout Process

| Parameter      | Type           | Default | Description                                                                                                                                    |
|----------------|----------------|---------|------------------------------------------------------------------------------------------------------------------------------------------------|
| `lineItems`    | `LineItem[]`   |         | List of the line items. See [Line Item](#line-item)                                                                                            |
| `tax`          | `Object`       |         | Map of tax rates (as string encoded decimals) to the portion of the price that was taxed with this rate                                        |
| `taxNet`       | `Object`       |         | Map of tax rates (as string encoded decimals) to the portion of the net price that was taxed with this rate                                    |
| `netPrice`     | `int`          |         | Net price                                                                                                                                  |
| `price`        | `int`          |         | Total price                                                                                                                                    |
| `state`        | `PaymentState` |         | The [Status of the payment process](api_checkout.md#payment-state). Only `pending`, `processing`, `successful`, `failed` are used in this case |
| `loyaltyCard`  | `String`       |         | The loyalty card identifier provided by the user                                                                                               |
| `processedBy`  | `String`       |         | String identifying the PoS which processes the checkout process                                                                                |
| `failedReason` | `String`       |         | Message describing the conditions under which the checkout process was marked as failed                                                        |

#### Line Item

| Parameter     | Type     | Default | Description                                                       |
|---------------|----------|---------|-------------------------------------------------------------------|
| `scannedCode` | `string` |         | Scanned code                                                      |
| `amount`      | `int`    |         | Number of products / packages                                     |
| `weight`      | `int`    | 0       | Weight of product in case of a weighable (a not packaged) product |
| `units`       | `int`    | 0       | Number of units in a package in case of bundle or piece product   |
| `price`       | `int`    |         | The base price of one product / unit                              |
| `totalPrice`  | `int`    |         | The total price                                                   |
| `taxRate`     | `string` |         | Tax rate as string encoded decimal                                |

#### Example
```
{
  "lineItems" : [
    {
      "scannedCode": "0000000000001",
      "totalPrice" : 798,
      "amount" : 2,
      "price" : 399,
      "taxRate" : 19,
      "sku" : "1"
    },
    {
      "scannedCode": "0000000000002"
      "price" : 1099,
      "totalPrice" : 1099,
      "amount" : 1,
      "taxRate" : 19
    }
  ],
  "tax" : {
    "19" : 7673
  },
  "taxNet" : {
    "19" : 40382
  },
  "netPrice" : 40382,
  "price" : 48055,
  "state": "pending",
  "loyaltyCard": "12345678",
  "processedBy": "pos-1"
}
```

## Get Checkout Process
`GET /{project}/pos/checkout/id/{id}`

Get the state of a Checkout Process.

**Required permissions** : checkoutRead for the checkout process

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## PUT Checkout Process
`PUT /{project}/pos/checkout/id/{id}`

Update the state of a Checkout Process.

**Required permissions** : updatePricingCheckoutProcess for the checkout process

### Request
**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process).

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------

## PATCH Checkout Process
`PATCH /{project}/pos/checkout/id/{id}`

Partially update the state of a Checkout Process.

The patch is applied according to [RFC 7386](https://tools.ietf.org/html/rfc7386).

**Required permissions** : updatePricingCheckoutProcess for the checkout process

### Request

**Content-Type** : application/merge-patch+json

**Data** : [Checkout Process](#checkout-process).

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Checkout Process](#checkout-process)

-----------
