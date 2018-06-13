# Products Management API

This documentation describes the snabble API endpoints related to the management and simple access of products.
These endpoints are available on the `api` subdomain. See [General API access](api_general.md) for general
information about api access.

### Single product operations

* [Get product](#get-product): `GET /{project}/products/sku/{sku}`
* [Create product](#create-product): `POST /{project}/products`
* [Update product](#update-product): `PUT /{project}/products/sku/{sku}`
* [Delete product](#delete-product): `DELETE /{project}/products/sku/{sku}`
* [Find product by PLU](#find-product-by-plu): `GET /{project}/products/plu/{plu}`
* [Find product by scannable code](#find-product-by-scannable-code): `GET /{project}/products/code/{code}`
* [Find product by weigh item id](#find-product-by-weigh-item-id): `GET /{project}/products/weighItemId/{code}`


### Multiple products

* [Batch import](#batch-import): `POST /{project}/products/_batch`
* [Request Products](#request-products): `GET /{project}/products`

### Other operations

* [Get product statistics](#get-statistics): `GET /{project}/produ^cts/statistics`

## Data Model

### Product Object
A single product is encoded as follows.

Example:
```
{
   "sku" : "1120325205",
   "name" : "Premium-Holzöl",
   "description": "farblos, 750ml",
   "subtitle" : "Aplina",
   "boost": 1,
   "taxCategory" : "normal",
   "depositProduct": null,
   "outOfStock" : false,
   "deleted" : false,
   "imageUrl" : "https://www.example.com/some/image.jpg",
   "productType" : "default",
   "controlIndication": 0.4,
   "forceControl": false,
   "saleRestriction": "min_age_18",
   "saleStop": true,
   "eans" : [
      "0654203316514"
   ],
   "price" : 1699,
   "discountedPrice" : 1499,
   "basePrice": "19,99 EUR / 1 Liter",
}
```

Product attributes:

| Parameter         | Type        | Default      | Description                                                                          |
|-------------------|-------------|--------------|--------------------------------------------------------------------------------------|
| sku               | string      |              | The unique id for identification of a the product                                    |
| name              | string      |              | The display name of the product                                                      |
| description       | string      | null         | A short description for the product                                                  |
| subtitle          | string      | null         | An additional title line for individual use (e.g. brand information)                 |
| boost             | int         | 0            | Order value for importance in views (higher is more)                                 |
| taxCategory       | string      | null         |                                                                                      |
| depositProduct    | string      | null         | The SKU of a corresponding product containing the associated deposit article         |
| outOfStock        | bool        | false        | Flag to indicate if the product is currently available in markets                    |
| deleted           | bool        | false        | Flag to indicate that a product does not exist any longer                            |
| imageUrl          | string      | null         | The full URL for a product image                                                     |
| productType       | string      | "default"    | Type of the product: "default", "weighable", "deposit"                               |
| controlIndication | number      | 0            | Indication: -1 no control needed, 1 high control indication                          |
| forceControl      | bool        | false        | Flag to indicate if a control is necessary                                           |
| saleRestriction   | string      | ""           | [Restriction rules](#sale-restrictions)                                                       |
| saleStop          | bool        | false        | Flag to indicate if there is a sale stop for this product                            |
| eans              | []string    | []           | List of scannable codes / barcodes which point to this product                       |
| price             | int         | 0            | The current standard price                                                           |
| discountedPrice   | int         | null         | The current price if the product is discounted.                                      |
| basePrice         | string      | ""           | Base price (e.g. price per liter) as label.                                          |
| weighing          | object      | null         | Additional information for weighable products                                        |

#### Sale restrictions

By now the only restriction rule is `min_age_`. There are six different min ages at the moment (`min_age_6, min_age_12, min_age_14, min_age_16, min_age_18, min_age_21`).


#### Weighable Products

The price of weighable product depents on information encoded into a
scannable code. There are three cases:

- The code contains a weight information

- The code contains the units in a prepackaged

- The code contains the actual price

This information is extracted using the `weighedItemIds`, which
represent templates for scannable codes recognized by the clients.

Weighing attributes:

| Parameter         | Type        | Default      | Description                                                                           |
|-------------------|-------------|--------------|---------------------------------------------------------------------------------------|
| weighedItemIds    | []string    | []           | Templates for the scannable codes which encode price or weight information            |
| pluSet            | []string    | []           | PLU, the short code to identify a weighable product                                   |
| weighByCustomer   | bool        | false        | Flag, if the product is prepackaged, ot the customer has to do weighting by himself   |
| referenceUnit     | string      | ""           | The unit in which the price attribute is calculated (e.g. "kg" where price is EUR/Kg) |
| encodingUnit      | string      | ""           | Unit which is used as encoding within the EAN (e.g. "g" when the EAN contains gramms) |

The supported units are:

| Abbreviation | Description                                                                                                    |
|--------------|----------------------------------------------------------------------------------------------------------------|
| kg           | Kilogram                                                                                                       |
| g            | Gram                                                                                                           |
| piece        | The encoded number should be interpreted as number of contained units, i.e. number of bread rolls in a package |
| price        | The encoded number should be interpreted as total price, i.e. price of a package of differen sausages          |

Weighing Example:

* **Product:** A bag with apples "Cripps Pink"
* **Base Price:** 3.90 / kg
* **Package Weight:** 720g
* **EAN-13:** 2323230007204 (Actual code on the bag after weighing)

```
{
    "weighedItemIds": ["2323230000000", "2727270000000"],
    "pluSet": ["4128"],
    "weighByCustomer": true,
    "referenceUnit": "kg",
    "encodingUnit": "g"
}
```

### Result Message

Result messages are simple status objects which are returned for batch operations.

| Parameter | Type        | Description                                                                           |
|-----------|-------------|---------------------------------------------------------------------------------------|
| sku       | string      | The product, referenced by the result message                                         |
| status    | string      | The processing status "ok", "error"                                                   |
| message   | string      | A human readeable message about the processing status                                 |

### Statistics Object

Statistics Objects are a simple representation of the product statistics.

| Parameter | Type        | Description                                                                           |
|-----------|-------------|---------------------------------------------------------------------------------------|
| count     | int      | The total product count                                                                  |
| created   | int      | The amount of newly created products                                                     |
| updated   | int      | The amount of updated products                                                           |
| deleted   | int      | The amount of deleted products                                                           |
| inStock   | int      | The amount of products that are in stock                                                 |


### Content Types

**Content-Type** : application/json

The payload is a JSON document of the specified type.

**Content-Type** : application/x-ndjson

The payload is a stream of JSON objects, one object per line.
See [ndjson-spec](https://github.com/ndjson/ndjson-spec) for more details.

-----------

## Get product
`GET /{project}/products/sku/{sku}`

Return one product by sku.

**Required permissions** : productsRead

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [product object](#product-object).

-----------

## Create product
`POST /{project}/products`

Create or update a product. If the product already exists, it will be updated.

**Required permissions** : productsWrite

### Request
**Content-Type** : application/json

**Data** : [product object](#product-object).

### Success Response `201 Created`

no content

-----------

## Update product
`PUT /{project}/products/sku/{sku}`

Create or update a product.
The sku in the url parameter and the JSON payload have to match.

**Required permissions** : productsWrite

### Request
**Content-Type** : application/json

**Data** : [product object](#product-object).

### Success Response `200 OK`

no content

-----------

## DELETE product
`DELETE /{project}/products/sku/{sku}`

Delete a product.

**Required permissions** : productsWrite

### Success Response `204 No Content`

no content

-----------

## Find product by PLU
`GET /{project}/products/plu/{plu}`

Returns one product by PLU

**Required permissions** : productsRead

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [product object](#product-object).

-----------

## Find product by scannable code
`GET /{project}/products/code/{code}`

Returns one product by scannable code

**Required permissions** : productsRead

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [product object](#product-object).

-----------

## Find product by weigh item id
`GET /{project}/products/weighItemId/{code}`

Returns one product by weigh item id

**Required permissions** : productsRead

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [product object](#product-object).

-----------

## Batch import

`POST /{project}/products/_batch`

Import a list of products in one http request.

**Required permissions** : productsWrite

### Request
**Content-Type** : application/x-ndjson

**Data** : JSON stream of [product objects](#product-object).

### Success Response `200 OK`

**Content-Type** : application/x-ndjson

**Data** : JSON stream of [result messages](#result-message).

-----------

## Request products
`GET /{project}/products`

Return all products of a project as JSON stream.

**Required permissions** : productsRead

### Request
**Parameters** :

All parameters are optional.

| Name  | Description |
| ------------- | ------------- |
| limit | Sets a limit of products returned. Must be a positive number. |
| q | Passes a query string. Will do a full text search in the product name and description and will perform a prefix search with the product SKU. All products found by either search will be returned. |


### Success Response `200 OK`

**Content-Type** : application/x-ndjson

**Data** : JSON stream of [product objects](#product-object).

-----------

## Get statistics
`GET /{project}/products/statistics`

Return simple statistics about the products in a time period ranging from a selected date until now.

**Required permissions** : productsRead

### Request
**Parameters** :

| Name  | Description |
| ------------- | ------------- |
| since |The starting date of the time period. This parameter is required.|


### Success Response `200 OK`

**Content-Type** : application/json

**Data** : JSON of [statistics object](#statistics-object).
