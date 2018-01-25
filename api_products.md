
# Products Management API

This documentation describes the snabble API endpoints related to the management and simple access of products.
All this endpoints are available within the `api` subdomain. See [General API access](api_common.md) for general
information in api access.

## Endpoints
### Endpoints for single product operations

* [Get product](#get-product): `GET /{project}/products/sku/{sku}`
* [Create product](#create-product): `POST /{project}/products`
* [Update product](#update-product): `PUT /{project}/products/sku/{sku}`
* [Delete product](#delete-product): `DELETE /{project}/products/sku/{sku}`

### Endpoints for multiple products

* [Batch import](#batch-import): `POST /{project}/products/_batch`
* [Request Products](#Request Products) `GET /{project}/products`

## Data Model

Content Types
### Product Object

Example:
```
{
   "sku" : "1120325205"
   "name" : "Premium-Holzöl",
   "description": "farblos, 750ml"
   "subtitle" : "Aplina",
   "boost": 1,
   "taxCategory" : "normal",
   "depositProduct": null,
   "weighing": null,
   "outOfStock" : false,
   "deleted" : false,
   "imageUrl" : "https://www.example.com/some/image.jpg",
   "productType" : "default",
   "eans" : [
      "0654203316514"
   ],
   "price" : 1699,
   "discountedPrice" : 1499,
   "basePrice": "19,99 EUR / 1 Liter"
}
```
   
## Details
### Batch import

Import a list of products in one http request.

**URL** : `/{project}/products/_batch`

**Method** : `POST`

**Required permissions** : productsWrite

### Request
**Conent-Type** : application/x-ndjson
**Data** : Json stream of [product objects](product-object).

## Success Response

**Code** : `200 OK`
**Conent-Type** : application/x-ndjson
**Data** : Json stream of [result messages](result-message).

