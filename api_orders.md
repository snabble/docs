# Orders API

This documentation describes the snabble API endpoints related to the
 access of orders. These endpoints are available on the `api`
 subdomain. See [General API access](api_general.md) for general
 information about api access.

### Endpoints

* [Get orders](#get-orders): `GET /{project}/orders`
* [Get order](#get-order): `GET /{project}/orders/{id}`

* [Get closings](#get-closings) `GET /{project}/closings`
* [Get closing](#get-closing) `GET /{project}/closings/shops/{shopID}/sequenceNumbers/{seqNum}`
* [Get closing log file](#get-closing-log-file) `GET /{project}/closings/logs/{id}`

* [Create schedule](#create-schedule) `POST /{project}/closings/schedules`
* [Get schedules](#get-schedules) `GET /{project}/closings/schedules`
* [Get schedule](#get-schedule) `GET /{project}/closings/schedules/id/{id}`
* [Update schedule](#update-schedule) `PUT /{project}/closings/schedules/id/{id}`
* [Delete schedule](#delete-schedule) `DELETE /{project}/closings/schedules/id/{id}`

## Model

### Order

| Path                   | Type             | Description                                                                                   |
|------------------------|------------------|-----------------------------------------------------------------------------------------------|
| `id`                   | `string`         | The id of the Order                                                                           |
| `project`              | `string`         | The id of the Project                                                                         |
| `date`                 | `date`           | The date on which the order was contracted                                                    |
| `createdAt`            | `date`           | The date on which the order was processed                                                     |
| `finalizedAt`          | `date`           | The date on which the order was finalized (i.e. the time the order was paid)                  |
| `clientID`             | `string`         | The app ID                                                                                    |
| `customer.loyaltyCard` | `string`         | The loyalty card number used                                                                  |
| `shopID`               | `string`         | ID of the in which the order was contracted                                                   |
| `shop.name`            | `string`         | Name of the Shop (see [Shops API](api_shops.md#shop))                                         |
| `shop.externalID`      | `string`         | An identifier for the provided by external sources (see [Shops API](api_shops.md#shop))       |
| `shop.street`          | `string`         | Street and number of the Shop (see [Shops API](api_shops.md#shop))                            |
| `shop.zip`             | `string`         | Zip of the Shop (see [Shops API](api_shops.md#shop))                                          |
| `shop.state`           | `string`         | State in which the Shop is situated (see [Shops API](api_shops.md#shop))                      |
| `shop.country`         | `string`         | Country in which the Shop is situated (see [Shops API](api_shops.md#shop))                    |
| `shop.city`            | `string`         | City in which the Shop is situated (see [Shops API](api_shops.md#shop))                       |
| `shop.phone`           | `string`         | Phone number of the Shop (see [Shops API](api_shops.md#shop))                                 |
| `shop.email`           | `string`         | Email address of the Shop (see [Shops API](api_shops.md#shop))                                |
| `paymentMethod`        | `string`         | The payment method used                                                                       |
| `paymentInformation`   | `Object`         | Payment dependent additional information                                                      |
| `paymentResult     `   | `Object`         | Payment gateway result object                                                                 |
| `paymentStatus`        | `string`         | The final Status of the associated payment process                                            |
| `lineItems`            | `LineItem[]`     | Line items of the order. For details see [Checkout API: Line Item](api_checkout.md#line-item) |
| `currency`             | `string`         | Currency of the project                                                                       |
| `price.price`          | `int`            | The total brutto price of the order                                                           |
| `price.netPrice`       | `int`            | The total netto price of the order                                                            |
| `price.tax`            | `map[string]int` | Mapping of tax rates on the portion of the price                                              |
| `price.taxNet`         | `map[string]int` | Mapping of tax rates sums all net prices of products with this rate up                        |
| `price.taxPre`         | `map[string]int` | Mapping of tax rates sums all pre tax prices of products with this rate up                    |
| `session`              | `string`         | The session ID                                                                                |


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
  "links": {
    "self": {
      "href": "/test-project-faa116/orders/99e13f9f-0412-4686-907a-331003b2a703"
    }
  }
}
```

### Closing

| Path                      | Type                     | Description                                                                             |
|---------------------------|--------------------------|-----------------------------------------------------------------------------------------|
| `project`                 | `string`                 | Project identifier                                                                      |
| `shopID`                  | `string`                 | Identifier of the shop                                                                  |
| `sequenceNumber`          | `int`                    | Sequence number of the closing                                                          |
| `createdAt`               | `date`                   | Date of creation. Formated in the RFC3339 format (ie. `2018-05-02T00:00:00-02:00`)      |
| `currency`                | `string`                 | Currency code of the closings currency                                                  |
| `lastOrderSequenceNumber` | `int`                    | Sequence number of the last order included in the closing                               |
| `shop.name`               | `string`                 | Name of the Shop (see [Shops API](api_shops.md#shop))                                   |
| `shop.externalID`         | `string`                 | An identifier for the provided by external sources (see [Shops API](api_shops.md#shop)) |
| `shop.street`             | `string`                 | Street and number of the Shop (see [Shops API](api_shops.md#shop))                      |
| `shop.zip`                | `string`                 | Zip of the Shop (see [Shops API](api_shops.md#shop))                                    |
| `shop.state`              | `string`                 | State in which the Shop is situated (see [Shops API](api_shops.md#shop))                |
| `shop.country`            | `string`                 | Country in which the Shop is situated (see [Shops API](api_shops.md#shop))              |
| `shop.city`               | `string`                 | City in which the Shop is situated (see [Shops API](api_shops.md#shop))                 |
| `shop.phone`              | `string`                 | Phone number of the Shop (see [Shops API](api_shops.md#shop))                           |
| `shop.email`              | `string`                 | Email address of the Shop (see [Shops API](api_shops.md#shop))                          |
| `taxShares`               | `TaxShare[]`             | List of [Tax Share](#tax-share)                                                         |
| `shareByPaymentMethod`    | `ShareByPaymentMethod[]` | List of [Share by Payment Method](#share-by-ayment-ethod)                               |
| `total`                   | `int`                    | The total sum of the closing                                                            |
| `taxNumber`               | `string`                 | Tax number                                                                              |
| `vatID`                   | `string`                 | Value added tax identifier                                                              |
| `companyName`             | `string`                 | Name of the company                                                                     |
| `companyAddress.city`     | `string`                 | Company official address city                                                           |
| `companyAddress.country`  | `string`                 | Company official address country                                                        |
| `companyAddress.street`   | `string`                 | Company official address street                                                         |
| `companyAddress.zip`      | `string`                 | Company official address zipcode                                                        |
| `orders`                  | `Order[]`                | List of [Order](#order) included in this closing                                        |
| `logs`                    | `ClosingLog[]`           | List of [Closing Log](#closing-log) written for this closing                            |


#### Links

| Relation  | Description                         |
|-----------|-------------------------------------|
| `receipt` | The Receipt generated for the order |


```
{
    "project" : "project",
    "shopID" : "aShop",
    "sequenceNumber" : 1234,

    "createdAt" : "2019-11-12T12:13:15Z",
    "currency" : "EUR",
    "lastOrderSequenceNumber" : 1234,

    "shop" : {
        "city" : "City",
        "country" : "Country",
        "countryCode" : "DEU",
        "email" : "Email",
        "externalID" : "ExternalID",
        "name" : "Name",
        "phone" : "Phone",
        "state" : "State",
        "street" : "Street",
        "zip" : "Zip"
    },

    "taxShares" : [ ... ],
    "shareByPaymentMethod" : [ ... ],
    "totalPrice" : 119,

    "taxNumber" : "TaxNumber",
    "vatID" : "TaxID",

    "companyName" : "CompanyName",
    "companyAddress" : {
        "city" : "City",
        "country" : "Country",
        "street" : "Street",
        "zip" : "Zip"
    },

    "orders": [ ... ],

    "logs": [ ... ],

    "links": {
        "self": {
            "href": "/project/closings/shops/aShop/sequenceNumbers/1234"
        },
        "receipt": {
            "href": "/project/receipts/id"
        }
    }
}
```

#### Tax Share

Represents a share of the total which was taxed with the given rate.

| Path    | Type     | Description                            |
|---------|----------|----------------------------------------|
| `rate`  | `string` | Decimal represantation of the tax rate |
| `net`   | `int`    | Netto portion                          |
| `tax`   | `int`    | Tax share                              |
| `total` | `int`    | Total share                            |

```
{
    "rate" : "19",
    "net" : 100,
    "tax" : 19,
    "total" : 119
}
```

#### Share by Payment Method

Represents a share of the total which was paid with the named method.

| Path            | Type     | Description                         |
|-----------------|----------|-------------------------------------|
| `paymentMethod` | `string` | Identifier of the  Payment Method   |
| `amount`        | `int`    | Amount paid with the Payment Method |


```
{
    "amount" : 119,
    "paymentMethod" : "sepa"
}
```

#### Closing Logs

| Path     | Type     | Description            |
|----------|----------|------------------------|
| `id`     | `int`    | Identifier of the log  |
| `format` | `string` | Fromat of the log file |
| `state` | `string` | State of the log (Values: `not_present` = The log is currently not created, `ongoing` = The log gets currently created, `present` = The log was sucessfully created, `failed` = The creation of the log is failed.) |

The `file` link of the object points to actual log content.

```
{
    "format" : "dfka_taxonomie_2_0",
    "id" : 10,
    "state" : "not_present",
    "links": {
        "file": {
            "href": "/project/closings/logs/10"
        }
    }
}
```

### Closing List

| Path       | Type         | Description                 |
|------------|--------------|-----------------------------|
| `closings` | `Closings[]` | List of [Closing](#closing) |

```
{
    "closings": [ ... ],
     "links": {
        "self": {
            "href": "/project/closings"
        }
    }
}
```


### Create Closing Schedule

| Path       | Type       | Description                                                                                          |
|------------|------------|------------------------------------------------------------------------------------------------------|
| `id`       | `int`      | Identifier                                                                                           |
| `project`  | `string`   | Project                                                                                              |
| `shop`     | `string`   | Identifier of the shop for which the closing should be created                                       |
| `mode`     | `string`   | Mode of the created Closing Schedule. Supported are: `includeUntilExecution`, `includeUntilScheduled`|
| `hour`     | `int`      | Hour (`0-24`) at which the closing should be created                                                 |
| `minute`   | `int`      | Minute (`0-60`) at which the closing should be created                                               |
| `days`     | `string[]` | Name of the days used (`Sunday`, `Monday`, `Tuesday`, `Wednesday`, `Thursday`, `Friday`, `Saturday`) |
| `location` | `string`   | [IANA Timezone Database](https://www.iana.org/time-zones) name of the location used                  |

```
{
    "id": 12,
    "project": "project",
    "shop": "1",
    "mode": "includeUntilExecution",
    "hour": 10,
    "minute": 30,
    "days": [
        "Monday",
        "Tuesday"
    ],
    "location": "Europe/Berlin",
    "links": {
        "self": {
            "href": "/project/closings/schedules/id/12"
        }
    }
}
```


### Create Closing Schedule List

| Path        | Type                      | Description        |
|-------------|---------------------------|--------------------|
| `schedules` | `CreateClosingSchedule[]` | Schedules returned |

```
{
    "schedules": [ ... ],
    "links": {
        "self": {
            "href": "/project/closings/schedules"
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


## Get closings

`GET /{project}/closings`

Get the list of closings of the project.

**Required permissions** : ordersRead for the project

### Request
**Parameters** :

| Name      | Default | Description                      |
|-----------|---------|----------------------------------|
| `page`    | `0`     | Number of the page to be fetched |
| `perPage` | `1000`  | Number of items per page         |

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : A [Closing List](#closing-list)


## Get closing

`GET /{project}/closings/shops/{shopID}/sequenceNumbers/{seqNum}`

Get a single closing.

**Required permissions** : ordersRead for the project

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : A [Closing](#closing)


## Get closing log file

`GET /{project}/closings/logs/{id}`

Get a single log file for a closing.

**Required permissions** : closingRead for the project

### Success Response `200 OK` or a http redirect

*Warning* : The service might send a redirect to the correct ressource!


## Create schedule

`POST /{project}/closings/schedules`

Create a new schedule.

**Required permissions** : closingSchedulesWrite for the project

### Request

**Data** : [Create Closing Schedule](#create-closing-schedule)

### Success Response `201 Created`

**Header** : `Location` contains a link to the created resource

## Get schedules

`GET /{project}/closings/schedules`

Get the list of schedules of the project.

**Required permissions** : createClosingSchedulesRead for the project

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : A [Create Closing Schedule List](#create-closing-schedule-list)


## Get schedule

`GET /{project}/closings/schedules/id/{id}`

Get a single schedule.

**Required permissions** : createClosingSchedulesRead for the project

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : A [Create Closing Schedule](#create-closing-schedule)


## Update schedule

`PUT /{project}/closings/schedules/id/{id}`

Update the schedule.

**Required permissions** : createClosingSchedulesWrite for the project

### Request

**Data** : [Create Closing Schedule](#create-closing-schedule)

### Success Response `200 OK`


## Delete schedule

`DELETE /{project}/closings/schedules/id/{id}`

Delete a schedule.

**Required permissions** : createClosingSchedulesWrite for the project

### Success Response `200 OK`
