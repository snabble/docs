# Pricing API

This documentation describes the snabble API endpoints related to the
management of product pricing configuration.  These endpoints are
available on the `api` subdomain. See [General API
access](api_general.md) for general information about api access.

## Operations

### Single pricing category operations

* [Get pricing category](#get-pricingcategory): `GET /{project}/pricing/categories/id/{id}`
* [Create pricing category](#create-pricingcategory): `POST /{project}/pricing/categories`
* [Update pricing category](#update-pricingcategory): `PUT /{project}/pricing/categories/id/{id}`

### Multiple pricing categories operations

* [Request pricing categories](#request-pricingcategory): `GET /{project}/pricing/categories`

### Single pricing operations

* [Get pricing](#get-pricing): `GET /{project}/pricing/products/sku/{sku}`
* [Delete pricing](#delete-pricing): `DELETE /{project}/pricing/products/sku/{sku}`
* [Update pricing](#update-pricing): `PUT /{project}/pricing/products/sku/{sku}`

### Multiple pricing operations

* [Batch update pricing](#batch-update-pricing): `POST /{project}/pricing/products/_batch`
* [Delete all pricings](#delete-all-pricings): `DELETE /{project}/pricing/products`

### Price operations

* [Delete price](#delete-price): `DELETE /{project}/pricing/products/sku/category/{categoryID)`
* [Batch update prices](#batch-update-prices): `POST /{project}/pricing/_batch`

## Data Model

### `PricingCategory` Object

Represents a named collection of shops which share the same pricing
configuration (i.e. prices).

Example:

```
{
  "id": "normal",
  "project": "project",
  "name": "normal prices",
  "priority": 2,
  "shops": [
    {"id": "shop-id-1"},
    {"id": "shop-id-2"}
  ],
  "links": {
    "self": {
      "href": "/project/pricing/categories/id/normal"
    }
  }
}
```

`PricingCategory` attributes:

| Parameter | Type   | Default | Description                                                                             |
|-----------|--------|---------|-----------------------------------------------------------------------------------------|
| id        | string |         | Unique id of the `PricingCategory`                                                      |
| project   | string |         | project id                                                                              |
| name      | string | null    | pricingCategory display name                                                            |
| priority  | int    |         | When resolving prices the categories are considered in descending order of priority     |
| shops     | array  |         | array of [`Shop`](#shop-object) which use this category                                 |
| links     | object | null    | The links                                                                               |

#### The `default` category

The category with the id `default` is used as a fallback. If the app
tries to determine the price of a products for a shop and the shop is
not associated with a category or the category has no price for the
product the price from the default category is used.

The price set through the existing products api is stored as the
default price.

### `Shop` Object

Represents a reference on a shop as defined by the [shops
api](Reference).

Example:
```
{ "id": "1" }
```

| Parameter | Type   | Default | Description             |
|-----------|--------|---------|-------------------------|
| id        | string |         | Unique id of the `Shop` |

### `PricingCategoryList` object

List of [`PricingCategory`](#pricingcategory-object).

Example:

```
{
  "links": {
    "self": {
      "href": "/project/pricing/categories"
    }
  },
  "categories": [
    {
      "id": "normal",
      "project": "project",
      "name": "normal prices",
      "shops": [
        {"id": "shop-id-1"},
        {"id": "shop-id-2"}
      ],
      "links": {
        "self": {
          "href": "/project/pricing/categories/id/normal"
        }
      }
    }
  ]
}
```

| Parameter  | Type  | Default | Description                |
|------------|-------|---------|----------------------------|
| categories | array |         | array of `PricingCategory` |


### `Pricing` object

Represents the information used to determine the actual price of a
product sold.

Example:
```
{
  "sku": "sku",
  "project": "project",
  "prices": [ /* array of Price */ ],
  "link": {
    "self": {
      "href": "/project/pricing/products/sku/some-sku"
    },
    "product": {
      "href": "/project/products/sku/some-sku"
    }
  }
}
```

| Parameter | Type   | Default | Description                       |
|-----------|--------|---------|-----------------------------------|
| sku       | string |         | sku of the product                |
| project   | string |         | project id                        |
| prices    | array  |         | array of [`Price`](#price-object) |

### `Price` Object

The actual price.

```
{
  "category": "normal",
  "listPrice": 199,
  "discountedPrice": 149,
  "customerCardPrice": 129,
  "basePrice": "14.90 €/kg"
}
```

| Parameter         | Type   | Description                                                   |
|-------------------|--------|---------------------------------------------------------------|
| category          | string | id of the `PricingCategory`                                   |
| listPrice         | int64  | list price of the product in this category                    |
| discountedPrice   | int64  | discounted price for the product in this category             |
| customerCardPrice | int64  | reduced price for customers owning a loyalty or customer card |
| basePrice         | string | base price for the product in this category                   |

### `PricingBatchUpdate` Object

Batch update for pricings.

Example:

```
{
    "op": "put',
    "item": {
        "sku": "a-product",
        "prices": [ … ]
    }
}
```
| Parameter | Type    | Description                                                   |
|-----------|---------|---------------------------------------------------------------|
| op        | string  | `put` to create or update a pricing, or `delete` to delete it |
| item      | Pricing | [A `Pricing` Object](#pricing-object)                         |

### `PricingBatchUpdateResultMessage` Object

Result messages are simple status objects which are returned for batch operations.

| Parameter | Type   | Description                                               |
|-----------|--------|-----------------------------------------------------------|
| sku       | string | The product referenced by the result message              |
| status    | string | The processing status: "ok" or "error"                    |
| message   | string | A human-readable message describing the processing status |


### `PriceBatchUpdate` Object

Batch update for prices.

Example:

```
{
    "sku": "a-product",
    "category": "normal",
    "listPrice": 199,
    "discountedPrice": 149,
    "customerCardPrice": 129,
    "basePrice": "14.90 €/kg"
}
```

| Parameter         | Type   | Description                                                   |
|-------------------|--------|---------------------------------------------------------------|
| sku               | string | sku of the product                                            |
| category          | string | id of the `PricingCategory`                                   |
| listPrice         | int64  | list price of the product in this category                    |
| discountedPrice   | int64  | discounted price for the product in this category             |
| customerCardPrice | int64  | reduced price for customers owning a loyalty or customer card |
| basePrice         | string | base price for the product in this category                   |

### `PriceBatchUpdateResultMessage` Object

Result messages are simple status objects which are returned for batch operations.

| Parameter | Type   | Description                                               |
|-----------|--------|-----------------------------------------------------------|
| sku       | string | The product referenced by the result message              |
| category  | string | id of the `PricingCategory`                               |
| status    | string | The processing status: "ok" or "error"                    |
| message   | string | A human-readable message describing the processing status |


## Operations

### Get `PricingCategory`

`GET /{project}/pricing/categories/id/{id}`

Returns one pricing category.

**Required permissions**: pricingCategoriesRead

#### Success Response 200 OK

**Content-Type** : application/json

**Data:** [PricingCategory](#pricingcategory-object)

-----------

### Create `PricingCategory`

`POST /{project}/pricing/categories`

Create a pricing category.

**Required permissions**: pricingCategoriesWrite

#### Request

**Content-Type** : application/json

**Data** : [PricingCategory](#pricingcategory-object)

#### Success Response 201 Created

**Content-Type** : application/json

**Data:** : [PricingCategory](#pricingcategory-object)

-----------

### Update `PricingCategory`

`PUT /{project}/pricing/categories/id/{id}`

Update a pricing category.

**Required permissions**: pricingCategoriesWrite


#### Request

**Content-Type** : application/json

**Data** : [PricingCategory](#pricingcategory-object)

#### Success Response 200 OK

**Content-Type** : application/json

**Data** : [PricingCategory](#pricingcategory-object)

-----------

### Request `PricingCategory`

`GET /{project}/pricing/categories`

Returns the list of pricing categories for the project.

**Required permissions**: pricingCategoriesRead

#### Success Response 200 OK

**Content-Type** : application/json

**Data:** [PricingCategoryList](#pricingcategorylist-object)

-----------

### Get `Pricing`

`GET /{project}/pricing/products/sku/{sku}`

Returns a pricing.

**Required permissions** : pricingRead

#### Success Response 200 OK

**Content-Type** : application/json

**Data** : [Pricing](#pricing-object)

-----------

### Delete `Pricing`

`DELETE /{project}/pricing/products/sku/{sku}`

Deletes a pricing.

**Required permissions** : pricingWrite

#### Success Response 200 OK

-----------

### Update `Pricing`

`PUT /{project}/pricing/products/sku/{sku}`

Update a pricing.

**Required permissions** : pricingWrite


#### Request

**Content-Type** : application/json

**Data** : [Pricing](#pricing-object)

#### Success Response 200 OK

**Content-Type** : application/json

**Data** : [Pricing](#pricing-object)


-----------

### Batch Update `Pricing`

`POST /{project}/pricing/products/_batch`

Update a batch of pricings.

**Required permissions**: pricingWrite

#### Request

**Maximal batch size** : 20000 items

**Content-Type** : application/x-ndjson

**Data** : JSON stream of [PricingBatchUpdate objects](#pricingbatchupdate-object)

#### Success Response 200 OK

**Content-Type** : application/x-ndjson

**Data** : JSON stream of [PricingBatchUpdateResultMessage](#pricingbatchupdateresultmessage-object)

-----------

### Delete all Pricings
`DELETE /{project}/pricing/products`

Delete all pricings.

**Required permissions**: pricingWrite

#### Success Response 200 OK

----------

### Delete Price

`DELETE /{project}/pricing/products/sku/{sku}/category/{categoryID}`

Deletes a single price for a single product in a single pricingCategory.

**Required permissions** : pricingWrite

#### Success Response 200 OK

-----------

### Batch Update `Prices`

`POST /{project}/pricing/_batch`

Update a batch of prices.

**Required permissions**: pricingWrite

#### Request

**Maximal batch size** : 20000 items

**Content-Type** : application/x-ndjson

**Data** : JSON stream of [PriceBatchUpdate objects](#pricebatchupdate-object)

#### Success Response 200 OK

**Content-Type** : application/x-ndjson

**Data** : JSON stream of [PriceBatchUpdateResultMessage](#pricebatchupdateresultmessage-object)
