# Integration with a virtual Point of Sale (vPOS)

snabble is able to use a virtual Point of Sale (vPOS) to integrate
into the systems of a retailer. The vPOS acts like a usual Point of
Sale system (PoS), but hands the processing of the checkout over to
snabble.

The vPOS is used in the checkout flow on two points: First it is used
to determine the price and further restrictions at the
beginning. Second, after completing the checkout the information on
the checkout is passed back to the vPOS. This allows it to trigger the
correct retailer internal processes.

## Data Model

Checkouts are represented through the following data structure:

```json
{
  "shopId": "1",
  "loyaltyCard": "...",
  "items": [
    {
      "id": "54ddafde-5207-11e9-b1c7-68f7286a148f",
      "sku": "1",
      "type": "default",
      "name": "Wine",
      "amount": 2,
      "price" : 119,
      "taxRate" : "19",
      "totalPrice": 238,
      "saleRestriction": "min_age_18",
      "scannedCode": "0000000000001"
    },
    {
      "id": "b33a8950-521e-11e9-934a-68f7286a148f",
      "sku": "bottle",
      "type": "deposit",
      "refersTo": "54ddafde-5207-11e9-b1c7-68f7286a148f",
      "name": "Deposit",
      "amount": "2",
      "price": 8,
      "taxRate": "0",
      "totalPrice": 16
    },
    {
      "id": "aded2a10-521f-11e9-96a0-68f7286a148f",
      "type": "discount",
      "refersTo": "54ddafde-5207-11e9-b1c7-68f7286a148f",
      "name": "Wine promotion",
      "amount": "1",
      "price": -38,
      "taxRate": "0",
      "totalPrice": 16
    },
    {
      "id": "5c8c5168-5207-11e9-bf60-68f7286a148f",
      "sku": "2",
      "type": "default",
      "amount": 1,
      "weight": 42,
      "weightUnit": "g",
      "name": "Apple",
      "amount": "1",
      "price": 199,
      "referenceUnit": "kg",
      "taxRate": "7",
      "totalPrice": 8,
      "scannedCode": "0000000000420"
    },
    {
      "id": "64769fc8-5207-11e9-8f9a-68f7286a148f",
      "sku": "3",
      "type": "default",
      "name": "Rolls",
      "amount": 1,
      "units": 6,
      "price": 32,
      "taxRate": "7",
      "totalPrice": 192,
      "scannedCode": "0000000000062"
    },
    {
      "id": "6a347656-5207-11e9-9d96-68f7286a148f",
      "sku": "4",
      "type": "default",
      "name": "Bread",
      "amount": 1,
      "price": 129,
      "taxRate": "7",
      "totalPrice": 129,
      "scannedCode": "0000000001298"
    }
  ],
  "taxShares" : [
    { "rate": "19", "net": 200, "share": 38, "total": 238 },
    { "rate": "7", "net": 307, "share": 22, "total": 329 },
    { "rate": "0", "net": -22, "share": 0, "total": -22 }
  ],
  "netPrice" : 485,
  "totalPrice" : 545,
  "orderId": "f30b0aa4-5210-11e9-a411-68f7286a148f",
  "externalCheckoutId": "1",
  "paymentInformation": {
    "mandateReference": "..."
  },
  "state": "completed",
  "receiptBuffer": "c25hYmJsZQo="
}
```

| Parameter            | Type         | Default | Description                                                                               |
|----------------------|--------------|---------|-------------------------------------------------------------------------------------------|
| `shopId`             | `string`     |         | Identifier of the shop                                                                    |
| `loyaltyCard`        | `string`     | `null`  | Loyalty card number of the customer                                                       |
| `customer`           | `Customer`   |         | Customer details                                                                          |
| `items`              | `LineItem[]` |         | List of the line items                                                                    |
| `taxShares`          | `TaxShare[]` | `null`  | List of tax shares                                                                        |
| `netPrice`           | `int`        | `null`  | Net price of the checkout                                                                 |
| `totalPrice`         | `int`        | `null`  | Total price of the checkout                                                               |
| `orderId`            | `string`     | `null`  | Identifier of the snabble order                                                           |
| `externalCheckoutId` | `string`     | `null`  | Identifier used by the vPOS to identify the checkout                                      |
| `paymentInformation` | `object`     | `null`  | Additional information provided by the payment system (i.e. transaction id, SEPA mandate) |
| `state`              | `string`     | `null`  | The state of the checkout (`open`, `completed`)                                           |
| `receiptBuffer`      | `string`     | `null`  | Base64 encdoded receipt file (either PDF or Windows Print Buffer)                         |
| `errors`             | `[]Error`    | `null`  | List of errors                                                                            |


#### Tax Shares

| Parameter | Type     | Default | Description                        |
|-----------|----------|---------|------------------------------------|
| `rate`    | `string` |         | Tax rate as string encoded decimal |
| `net`     | `int`    |         | Net portion                        |
| `share`   | `int`    |         | Tax share for this rate            |
| `total`   | `int`    |         | The total                          |


#### Line Item

| Parameter         | Type      | Default   | Description                                                                                                                  |
|-------------------|-----------|-----------|------------------------------------------------------------------------------------------------------------------------------|
| `id`              | `string`  |           | Identifier of the line item                                                                                                   |
| `type`            | `string`  | `default` | Type of the line item (`default`, `deposit`, `giveaway`, `discount`)                                                         |
| `refersTo`        | `string`  | `null`    | line item that is related to this one (i.e. if the line item represents a deposit the id of the line item which requires it) |
| `sku`             | `string`  | `null`    | SKU of the product                                                                                                           |
| `amount`          | `int`     |           | Number of products / packages                                                                                                |
| `weight`          | `int`     | `0`       | Weight purchased                                                                                                             |
| `weightUnit`      | `string`  |           | Unit of the weight                                                                                                           |
| `referenceUnit`   | `string`  |           | Reference unit for weighable item, Details: [Product API: weighable products](api_products.md#weighable-products)            |
| `units`           | `int`     | `0`       | Number of units in a package in case of bundle or piece product                                                              |
| `price`           | `int`     |           | Price of the product for single unit, piece, weightUnit, etc                                                                 |
| `totalPrice`      | `int`     |           | Total price of the line item                                                                                                 |
| `name`            | `string`  |           | Name of the product                                                                                                          |
| `taxRate`         | `string`  |           | Tax rate as string encoded decimal                                                                                           |
| `scannedCode`     | `string`  |           | Scanned code                                                                                                                 |
| `saleRestriction` | `string`  |           | [Restrictions](api_products.md#sale-restrictions) that apply to the line item                                                |
| `errors`          | `[]Error` | `null`    | List of errors                                                                                                               |


### Error

In case of an error the checkout or the line item might contain a list
of error objects. Such an object consists of `type` string and an
informational `message`.

```json
{
  "items": [
    {
      "id": "00883a9e-520c-11e9-a629-68f7286a148f",
      "errors": [
        {
          "type": "invalid_line_item",
          "message": "Invalid line item"
        }
      ]
    }
  ],
  "errors": [
    {
      "type": "invalid_cart",
      "message": "Invalid line item"
    }
  ]
}
```

| Parameter | Type     | Default | Description                          |
|-----------|----------|---------|--------------------------------------|
| `type`    | `string` |         | Type of the error                    |
| `message` | `string` | `null`  | Optional informational error message |

##### Line Item Errors

| Error types         | Description                           |
|---------------------|---------------------------------------|
| `unknown_product`   | Product could not be identified       |
| `sale_stop`         | Product can't be sold                 |
| `invalid_line_item` | The line item is semantically invalid |
| `other`             | Other errors                          |

##### Cart Errors

| Error types            | Description                            |
|------------------------|----------------------------------------|
| `invalid_cart_items`   | Some cart items are invalid            |
| `service_unavailable`  | The service is currently not available |
| `invalid_loyalty_card` | The loyalty card is invalid            |

## Endpoints

### Authorization

The Service sends an `Authorization` header as described in [RFC 7235
Section 4.2](https://tools.ietf.org/html/rfc7235#section-4.2). The
used authentication scheme is `Bearer`. Additionally authorization
through TLS 1.2 / 1.3 certificates is supported.

### Obtain Checkout Information

This endpoint is called if the system needs to complete the
information in a checkout. It sends an incomplete checkout and the
service should respond with a completed checkout. The checkout misses
the prices, the tax rates, the names and the sale restrictions.

The service responds with a completed checkout containing the missing
information. Further it might add additional line items that represent
promotions (like discounts or additional items) or deposits.

#### Request

```json
POST / HTTP 1.1
Accept: application/json

{
  "shopId": "1",
  "loyaltyCard": "...",
  "items": [
    {
      "id": "54ddafde-5207-11e9-b1c7-68f7286a148f",
      "sku": "1",
      "type": "default",
      "amount": 2,
      "scannedCode": "0000000000001"
    }
  ]
}
```

#### Response

```json
HTTP/1.1 201 Created
Content-Type: application/json

{
  "shopId": "1",
  "loyaltyCard": "...",
  "items": [
    {
      "id": "54ddafde-5207-11e9-b1c7-68f7286a148f",
      "sku": "1",
      "type": "default",
      "name": "Product",
      "amount": 2,
      "price" : 119,
      "taxRate" : "19",
      "totalPrice": 238,
      "saleRestriction": "min_age_18",
      "scannedCode": "0000000000001"
    }
  ],
  "taxShares" : [
    { "rate": "19", "net": 200, "share": 38, "total": 238 }
  ],
  "netPrice" : 200,
  "totalPrice" : 238
}
```

##### Status Codes

* `200 OK`, `201 Created`: The information was completed and the
  checkout is approved for processing
* `400 Bad Request`: The information could not be completed or the
  checkout is not approved for processing

### Place completed Checkout

After snabble has processed the checkout it sends a request to the
vPOS. This request contains all information on the checkout.

#### Request

The service sends the information as obtained before and adds the
snabble `orderId` and information on the payment. Further it sets the
`completed` flag to true.

```json
POST / HTTP 1.1
Accept: application/json

{
  "shopId": "1",
  "items": [
    {
      "id": "54ddafde-5207-11e9-b1c7-68f7286a148f",
      "sku": "1",
      "type": "default",
      "name": "Product",
      "amount": 2,
      "price" : 119,
      "taxRate" : "19",
      "totalPrice": 238,
      "saleRestriction": "min_age_18",
      "scannedCode": "0000000000001"
    }
  ],
  "taxShares" : [
    { "rate": "19", "net": 200, "share": 38, "total": 238 }
  ],
  "netPrice" : 200,
  "totalPrice" : 238,

  "orderId": "f30b0aa4-5210-11e9-a411-68f7286a148f",
  "paymentInformation": {
    "mandateReference": "..."
  },
  "state": "completed"
}
```

#### Response

The endpoints responds with the checkout. Optionally it might send a
property `externalCheckoutId`. These are stored. Further it might
provide a receipt through the `receiptBuffer` property.

```json
HTTP/1.1 201 Created
Content-Type: application/json

{
  // all fields as above
  "externalCheckoutId": "1",
  "receiptBuffer": "c25hYmJsZQo="
}
```

##### Status Codes

* `200 OK`, `201 Created`: The Checkout was processed by the vPOS
