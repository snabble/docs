# Orders API

This documentation describes the snabble API endpoints related to the
 access of orders. These endpoints are available on the `api`
 subdomain. See [General API access](api_general.md) for general
 information about api access.

### Endpoints

* [Get orders](#get-orders): `GET /{project}/orders`
* [Get order](#get-order): `GET /{project}/orders/{id}`
* [Get order statistics](#get-order-statistics): `GET /{project}/orders/statistics` (**Deprecated**)

## Model

### Order

| Path                  | Type             | Description                                                                                   |
|-----------------------|------------------|-----------------------------------------------------------------------------------------------|
| `id`                  | `string`         | The id of the Order                                                                           |
| `project`             | `string`         | The id of the Project                                                                         |
| `date`                | `date`           | The date on which the order was contracted                                                    |
| `createdAt`           | `date`           | The date on which the order was processed                                                     |
| `clientID`            | `string`         | The app ID                                                                                    |
| `customer.loyaltyCard`| `string`         | The loyalty card number used
| `shopID`              | `string`         | ID of the in which the order was contracted                                                   |
| `shop.name`           | `string`         | Name of the Shop (see [Shops API](api_shops.md#shop))                                         |
| `shop.externalID`     | `string`         | An identifier for the provided by external sources (see [Shops API](api_shops.md#shop))       |
| `shop.street`         | `string`         | Street and number of the Shop (see [Shops API](api_shops.md#shop))                            |
| `shop.zip`            | `string`         | Zip of the Shop (see [Shops API](api_shops.md#shop))                                          |
| `shop.state`          | `string`         | State in which the Shop is situated (see [Shops API](api_shops.md#shop))                      |
| `shop.country`        | `string`         | Country in which the Shop is situated (see [Shops API](api_shops.md#shop))                    |
| `shop.city`           | `string`         | City in which the Shop is situated (see [Shops API](api_shops.md#shop))                       |
| `shop.phone`          | `string`         | Phone number of the Shop (see [Shops API](api_shops.md#shop))                                 |
| `shop.email`          | `string`         | Email address of the Shop (see [Shops API](api_shops.md#shop))                                |
| `paymentMethod`       | `string`         | The payment method used                                                                       |
| `paymentInformation`  | `Object`         | Payment dependent additional information                                                      |
| `paymentResult     `  | `Object`         | Payment gateway result object                                                                 |
| `paymentStatus`       | `string`         | The final Status of the associated payment process                                            |
| `lineItems`           | `LineItem[]`     | Line items of the order. For details see [Checkout API: Line Item](api_checkout.md#line-item) |
| `currency`            | `string`         | Currency of the project                                                                       |
| `price.price`         | `int`            | The total brutto price of the order                                                           |
| `price.netPrice`      | `int`            | The total netto price of the order                                                            |
| `price.tax`           | `map[string]int` | Mapping of tax rates on the portion of the price                                              |
| `price.taxNet`        | `map[string]int` | Mapping of tax rates sums all net prices of products with this rate up                        |
| `price.taxPre`        | `map[string]int` | Mapping of tax rates sums all pre tax prices of products with this rate up                    |
| `session`             | `string`         | The session ID


Example:

```
{
  "id": "99e13f9f-0412-4686-907a-331003b2a703",
  "project": "test-project-faa116",
  "clientID": "app-id-12345",
  "date": "2018-06-05T12:25:06.821Z",
  "createdAt": "2018-06-05T12:25:07.519Z",
  "shopID": "128",
  "shop": {
    "externalID": test",
    "street": "Street 1",
    "zip": "12345",
    "state": "State",
    "country": "Country",
    "city": "City",
    "phone": "+49123456",
    "email": "test@test.de",
    "name": "Integration Test"
  },
  customer: {
    "loyaltyCard": "12354567"
  },
  "paymentMethod": "cash",
  "paymentState": "successful",
  "lineItems": [
    {
      "sku": "20001",
      "name": "Pommes,
      "amount": 2,
      "price": 107,
      "taxRate": "7",
      "totalPrice": 214
    }
  ],
  "price": {
    "price": 214,
    "netPrice": 200,
    "tax": {
      "7": 14
    },
    "taxNet": {
      "7": 200
    },
    "taxPre": {
      "7": 214
    }
  },
  "session": "asdaf24u-0124-77a4-77a8-ada100011112",
  "currency": "EUR",
  "checkoutProcess": {
    // see checkout documentation
  },
  "links": {
    "self": {
      "href": "/test-project-faa116/orders/99e13f9f-0412-4686-907a-331003b2a703"
    }
  }
}
```

### Order Statistic

(**Deprecated**)

| Path            | Type   | Description                        |
|-----------------|--------|------------------------------------|
| `count`         | `int`  | Number of orders                   |
| `total`         | `int`  | Total price of orders              |
| `items[].date`  | `date` | Date of the item                   |
| `items[].count` | `int`  | Number of orders on that date      |
| `items[].total` | `int`  | Total price of orders on that date |

```
{
    "count": 44,
    "total": 2139104,
    "items": [
        {
            "count": 0,
            "date": "2018-06-03T00:00:00Z",
            "total": 0
        },
        {
            "count": 44,
            "date": "2018-06-04T00:00:00Z",
            "total": 2139104
        },
        {
            "count": 0,
            "date": "2018-06-05T00:00:00Z",
            "total": 0
        }
    ],
    "links": {
        "self": {
            "href": "/test-project-faa116/orders/statistics"
        }
    }
}
```

## Get orders
`GET /{project}/orders`

Get the list of orders of the project.

Orders can be queried either by date or by shop.

**Required permissions** : ordersRead for the project

### Request
**Parameters** :

| Name              | Description                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------|
| `date`            | Date for which the orders should be returned. Formated in the RFC3339 format (ie. `2018-05-02T00:00:00-02:00`) |
| `shopID`          | ID of the shop                                                                                                  |
| `shop.externalID` | External ID of the shop as provided through the [Shops API](api_shops.md#shop)                                  |

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : List of [Orders](#order)

## Get order
`GET /{project}/orders/{id}`

Get a order.

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Order](#order)

## Get order statistics
`GET /{project}/orders/statistics?from={fromDate}&to={toDate}`

(**Deprecated**)

Get aggregates on orders for the given time range. Provides the total
number of orders and the transaction volume for each day in the time
range as well as for the whole periode.

The timezone is determined by the ones given by the `from` and `to`
date.

### Request
**Parameters** :

| Name   | Description                                                                                              |
|--------|----------------------------------------------------------------------------------------------------------|
| `from` | Start of the time periode (inclusive). Formated in the RFC3339 format (ie. `2018-05-02T00:00:00-02:00`) |
| `to`   | End of the time periode (exclusive). Formated in the RFC3339 format (ie. `2018-05-02T00:00:00-02:00`)   |

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Order Statistic](#order-statistic)
