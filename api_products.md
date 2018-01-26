
# Products Management API

This documentation describes the snabble API endpoints related to the management and simple access of products.
All this endpoints are available within the `api` subdomain. See [General API access](api_common.md) for general
information in api access.

### Single product operations

* [Get product](#get-product): `GET /{project}/products/sku/{sku}`
* [Create product](#create-product): `POST /{project}/products`
* [Update product](#update-product): `PUT /{project}/products/sku/{sku}`
* [Delete product](#delete-product): `DELETE /{project}/products/sku/{sku}`

### Multiple products

* [Batch import](#batch-import): `POST /{project}/products/_batch`
* [Request Products](#request-products): `GET /{project}/products`

## Data Model

### Product Object
A single product is encoded as follows.

Example:
```
{
   "sku" : 1120325205,
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
   "eans" : [
      "0654203316514"
   ],
   "price" : 1699,
   "discountedPrice" : 1499,
   "basePrice": "19,99 EUR / 1 Liter",
   "weighing": null
}
```

Product attributes:

| Parameter         | Type        | Default      | Description                                                                          |
|-------------------|-------------|--------------|--------------------------------------------------------------------------------------|
| sku               | int         |              | The uniq id for identification of a the product                                      |
| name              | string      |              | The main display name of a the product                                               |
| description       | string      | null         | A short description for the product                                                  |
| subtitle          | string      | null         | An additional title line for individual use (e.g. brand information)                 |
| boost             | int         | 0            | Order value for importance in views (higher is more)                                 |
| taxCategory       | string      | null         |                                                                                      |
| depositProduct    | int         | null         | The sku of a corresponding product, which contains the belonging eposit article      |
| outOfStock        | bool        | false        | Flag to indicate, if the product is currently available in markets                   |
| deleted           | bool        | false        | Flag to indicate, that a product does not exist any longer                           |
| imageUrl          | string      | null         | The full url on an product image                                                     |
| productType       | string      | "default"    | Type of the product: "default" "weighable", "deposit"                                |
| eans              | []string    | []           | List of scannable codes / barcodes which pint to the this product                    |
| price             | int         | 0            | The current standard price                                                           |
| discountedPrice   | int         | null         | The current price, if the product is discounted.                                     |
| basePrice         | string      | ""           | Base price (e.g. price per liter) as label.                                          |
| weighing          | object      | null         | Information to calculate the SKUs it the project is weighable                        |

Weighing attributes:

| Parameter         | Type        | Default      | Description                                                                           |
|-------------------|-------------|--------------|---------------------------------------------------------------------------------------|
| weighedItemIds    | []string    | []           | Templates for the eans with encoded price or weight information                       |
| pluSet            | []string    | []           | PLU, the short code to identiy a weighable product                                    |
| weighByCustomer   | bool        | false        | Flag, if the product is prepackaged, ot the customer has to do weighting by himself   |
| referenceUnit     | string      | ""           | The unit in which the price attribute is calculated (e.g. "kg" where price is EUR/Kg) |
| encodingUnit      | string      | ""           | Unit, which is used as encoding within the ean (e.g. "g" when the ean contains gramms)|


Weighing Example:

* **Product:** A bag with apples
* **Apple-Price:** 3.90 / kg
* **Package Weight:** 720g
* **EAN 13:** 2323230007204 (Actual code on the bag after weighing)

```
{
    "weighedItemIds": ["2323230000000", "2727270000000"],
    "pluSet": ["23"],
    "weighByCustomer": true,
    "referenceUnit": "kg",
    "encodingUnit": "g"
}
```

### Result Message

Result messages are simple status object which are returned for batch operations.

| Parameter | Type        | Description                                                                           |
|-----------|-------------|---------------------------------------------------------------------------------------|
| sku       | int         | The product, referenced by the result message                                         |
| status    | string      | The processing status "ok", "error"                                                   |
| message   | string      | A human readeable message about the processing status                                 |

### Content Types

**Conent-Type** : application/json

The payload is a json document, of the specified type.

**Conent-Type** : application/x-ndjson

The payload is a stream of JSON objects. One object per line.
See [ndjson-spec](https://github.com/ndjson/ndjson-spec) for mor details.

## Get product
`GET /{project}/products/sku/{sku}`

Return one product by sku.

**Required permissions** : productsRead

### Success Response `200 OK`

**Conent-Type** : application/json

**Data** : [product object](#product-object).

## Create product
`POST /{project}/products`

Create or update a product. If the product already exists, it will be updated.

**Required permissions** : productsWrite

### Request
**Conent-Type** : application/json

**Data** : [product object](#product-object).

### Success Response `201 Created`

no content

## Update product
`PUT /{project}/products/sku/{sku}`

Create or update a product.
the sku in the url parameter and the JSON payload have to match.

**Required permissions** : productsWrite

### Request
**Conent-Type** : application/json

**Data** : [product object](#product-object).

### Success Response `200 OK`

no content

## DELETE product
`DELETE /{project}/products/sku/{sku}`

Delete a product.

**Required permissions** : productsWrite

### Success Response `204 No Content`

no content

-----------

## Batch import

`POST /{project}/products/_batch`

Import a list of products in one http request.

**Required permissions** : productsWrite

### Request
**Conent-Type** : application/x-ndjson

**Data** : Json stream of [product objects](#product-object).

### Success Response `200 OK`

**Conent-Type** : application/x-ndjson

**Data** : Json stream of [result messages](#result-message).

## Request products
`GET /{project}/products`

Return all products of a project as json stream.

**Required permissions** : productsRead

### Success Response `200 OK`

**Conent-Type** : application/x-ndjson

**Data** : Json stream of [product objects](#product-object).

