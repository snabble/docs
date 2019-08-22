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


### Multiple products

* [Batch import](#batch-import): `POST /{project}/products/_batch`
* [Request products](#request-products): `GET /{project}/products`
* [Delete products](#delete-products): `DELETE /{project}/products`

### Other operations

* [Get product statistics](#get-statistics): `GET /{project}/products/statistics`

## Data Model

A product represents a good that can be bought in a shop. The product
contains all data which is needed to perform the checkout process.

Products might be prepackaged, like a box of pasta, which is
identified by a code (ie. an EAN-13) that is printed on the
packaging. In some cases the bought quantity is not known
upfront. These products are modeled as weighable products (which also
include products that are sold by other measures). Finally, there are
deposit products and products that bundle other products, ie. a box of
water bottles.

### Weighable Products

The term weighable product is not precise. Weighable products should
be used to map all kinds of not prepackaged products. These might be
products like fruits that are priced by weight, but also ropes that
are priced by their length or milk that is priced by volume. The
mechanism can also be used to model bundles like a bag of bread rolls
or instore EANs simply naming the price of the product.

The price of a weighable product depends on information encoded into a
scannable code. There are three cases:

- The code contains the measurement in the encoding unit. This
  measurement gets converted into the reference unit which allows the
  calculation of the price

- The code contains the units in a prepackaged container or bag

- The code contains the price

This information is extracted using the [Code
Templates](#code-template) described below.

#### Supported Units

The supported units are:

| Abbreviation | Description                                                                                                    |
|--------------|----------------------------------------------------------------------------------------------------------------|
| ml           | Milliliter                                                                                                     |
| cl           | Centiliter                                                                                                     |
| dl           | Deciliter                                                                                                      |
| l            | Liter                                                                                                          |
| m3           | Cubic meter                                                                                                    |
| cm3          | Cubic centimeter                                                                                               |
| m2           | Square meter                                                                                                   |
| m2e-1        | Square meter × 10e-1                                                                                           |
| dm2          | Square decimeter                                                                                               |
| m2e-3        | Square meter × 10e-3                                                                                           |
| cm2          | Square centimeter                                                                                              |
| mm           | Millimeter                                                                                                     |
| cm           | Centimeter                                                                                                     |
| dm           | Decimeter                                                                                                      |
| m            | Meter                                                                                                          |
| g            | Gram                                                                                                           |
| dag          | Decagram                                                                                                       |
| hg           | Hectogram                                                                                                      |
| kg           | Kilogram                                                                                                       |
| t            | Tonne                                                                                                          |
| piece        | The encoded number should be interpreted as number of contained units, i.e. number of bread rolls in a package |
| price        | The encoded number should be interpreted as total price, i.e. price of a package of different sausages         |


### Code Template

Code templates are a way to extract information that is embeded into a
scanned code. For example a instore EAN-13 has the format
`2xxxxxCeeeeeC` where the five `x` encode an identfier for the
product, the five `e` encode the the information and the `C` are the
checksums defined by the standard. With this and the additional
information contained in the product, it is possible to calculate the
price.

For example: Given the scanned code `2123455005005`. By using the
template it is possible to extract the identifier `12345` and the
embeded information `00500`. A product lookup might yield, that this
code belongs to the product Banana which has a price of 2.00 €, `g` as
encoding unit and `kg` as reference unit. With this information it is
possible to calculate the price: `00500` → 500g → 0,5 kg × 2.00 € → 1
€.

| Template             | Description                                                      |
|----------------------|------------------------------------------------------------------|
| `default`            | The default template, matches anything                           |
| `ean14_code128`      | An EAN-14 embedded in a Code128 with Application Identifier "01" |
| `ean13_instore`      | An in-store EAN-13 with embedded data, but no internal checksum  |
| `ean13_instore_chk`  | An in-store EAN-13 with embedded data and internal checksum      |
| `german_print`       | German newspapers/magazines with embedded price                  |

### Deposits

All products might reference a deposit, which is automatically added
to shopping cart if an user buys the products. Deposit products can
not be bought on their own.

### Bundles

Bundles are products that contain other products, ie. a crate of beer
bottles.

### Sale restrictions

The supported sale restrictions are:

| Name        | Description                                                                                                    |
|-------------|----------------------------------------------------------------------------------------------------------------|
| `min_age_*` | Minimum age. Possible values `min_age_6`, `min_age_12`, `min_age_14`, `min_age_16`, `min_age_18`, `min_age_21` |
| `fsk`       | Product was rated by the german "Freiwillige Selbstkontrolle der Filmwirtschaft" (FSK)                         |


### Product Object

A product has two different representations. The [first `Product
Object v2`](#product-object-v2) is newer and supports more
features. The [second `Product Object v1`](#product-object-v1) is
deprecated and should not be used anymore.

The service uses content negotiation to distinguish between the
representations. Please use
[`Accept`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept)
and
[`Content-Type`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)
headers with the MIME types named below.

#### Product Object v2

*MIME type*: `application/vnd.snabble.product.v2+json`

Example:
```
{
   "sku" : "1120325205",
   "project": "project",
   "name" : "Premium-Holzöl",
   "description": "farblos, 750ml",
   "subtitle" : "Aplina",
   "taxCategory" : "normal",
   "imageUrl" : "https://www.example.com/some/image.jpg",
   "productType" : "default",
   "controlIndication": 0.4,
   "saleRestriction": "min_age_18",
   "saleStop": true,
   "scanMessage": "multipack-2",
   "codes": [
       {
           "code": "0654203316514",
           "transmissionCode": "1234203316514",
           "template": "default"
       }
   ]
}
```

Product attributes:

| Parameter           | Type       | Default   | Description                                                                                                   |
|---------------------|------------|-----------|---------------------------------------------------------------------------------------------------------------|
| `sku`               | `string`   |           | The unique id for identification of the product                                                                |
| `project`           | `string`   |           | The project                                                                                                   |
| `name`              | `string`   | ""        | The display name of the product                                                                               |
| `description`       | `string`   | null      | A short description of the product                                                                            |
| `subtitle`          | `string`   | null      | An additional title line for individual use (e.g. brand information)                                          |
| `imageUrl`          | `string`   | null      | The full URL for a product image                                                                              |
| `depositProduct`    | `string`   | null      | The SKU of  the associated deposit product                                                                    |
| `bundledProduct`    | `string`   | null      | The SKU of the product contained in the bundle                                                                |
| `outOfStock`        | `bool`     | false     | Flag to indicate if the product is currently available in markets                                             |
| `deleted`           | `bool`     | false     | Flag to indicate that a product does not exist any longer                                                     |
| `taxCategory`       | `string`   | null      | Identifier of the tax category                                                                                 |
| `weighByCustomer`   | `bool`     | false     | Flag, if the product is prepackaged, or the customer has to do weighting by himself                           |
| `referenceUnit`     | `string`   | null      | The [unit](#supported-units) in which the price attribute is calculated (e.g. "kg" where price is EUR/Kg)     |
| `encodingUnit`      | `string`   | null      | [Unit](#supported-units) which is used as encoding within the EAN (e.g. "g" when the EAN contains grams)      |
| `codes`             | `[]object` | []        | [Array of code objects](#code-object-v2)                                                                      |
| `productType`       | `string`   | "default" | Type of the product: "default", "weighable", "deposit"                                                        |
| `controlIndication` | `number`   | 0         | Indication: -1 no control needed, 1 high control indication                                                   |
| `forceControl`      | `bool`     | false     | Flag to indicate if a control is necessary                                                                    |
| `saleRestriction`   | `string`   | null      | [Restriction rules](#sale-restrictions)                                                                       |
| `saleStop`          | `bool`     | false     | Flag to indicate if there is a sale stop for this product                                                     |
| `pluSet`            | `[]string` | null      | PLU, the short code to identify a weighable product                                                           |
| `scanMessage`       | `string`   | null      | Identifier of a message shown to the user after scanning a product (e.g. a product has more than one package ) |


#### Code Object v2

| Name               | Type     | Default   | Description                                                                                                        |
|--------------------|----------|-----------|--------------------------------------------------------------------------------------------------------------------|
| `code`             | `string` | ""        | Scannable code / barcode                                                                                           |
| `template`         | `string` | "default" | Identifier of the [code template](#code-template) used                                                              |
| `transmissionCode` | `string` | null      | In case the POS cannot handle the scannable code / barcode, it contains a POS friendly code                        |
| `encodingUnit`     | `string` | null      | [Unit](#supported-units) which is used as encoding within the scanned code. Overwrites the property of the product |


#### Product Object v1

**Deprecated**: Please, use the model v2 and the [Pricing API](api_pricing.md).

*MIME type*: `application/vnd.snabble.product.v1+json`

Example:
```
{
   "sku" : "1120325205",
   "name" : "Premium-Holzöl",
   "description": "farblos, 750ml",
   "subtitle" : "Aplina",
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
   "codes": [
       {
           "code": "0654203316514",
           "transmissionCode": "1234203316514"
       }
   ]
   "price" : 1699,
   "discountedPrice" : 1499,
   "basePrice": "19,99 EUR / 1 Liter",
}
```

Product attributes:

| Parameter           | Type       | Default   | Description                                                                                             |
|---------------------|------------|-----------|---------------------------------------------------------------------------------------------------------|
| `sku`               | `string`   |           | The unique id for identification of the product                                                         |
| `name`              | `string`   | ""        | The display name of the product                                                                         |
| `description`       | `string`   | null      | A short description of the product                                                                      |
| `subtitle`          | `string`   | null      | An additional title line for individual use (e.g. brand information)                                    |
| `taxCategory`       | `string`   | null      | Identifier of the tax category                                                                          |
| `depositProduct`    | `string`   | null      | The SKU of  the associated deposit product                                                              |
| `bundledProduct`    | `string`   | null      | The SKU of the product contained in the bundle                                                          |
| `outOfStock`        | `bool`     | false     | Flag to indicate if the product is currently available in markets                                       |
| `deleted`           | `bool`     | false     | Flag to indicate that a product does not exist any longer                                               |
| `imageUrl`          | `string`   | null      | The full URL for a product image                                                                        |
| `productType`       | `string`   | "default" | Type of the product: "default", "weighable", "deposit"                                                  |
| `controlIndication` | `number`   | 0         | Indication: -1 no control needed, 1 high control indication                                             |
| `forceControl`      | `bool`     | false     | Flag to indicate if a control is necessary                                                              |
| `saleRestriction`   | `string`   | null      | [Restriction rules](#sale-restrictions)                                                                 |
| `saleStop`          | `bool`     | false     | Flag to indicate if there is a sale stop for this product                                               |
| `eans`              | `[]string` | []        | *Deprecated: Please use codes instead.* List of scannable codes / barcodes which point to this product. |
| `codes`             | `[]object` | []        | [Array of code objects](#code-object)                                                                   |
| `price`             | `int`      | null      | The current standard price                                                                              |
| `discountedPrice`   | `int`      | null      | The current price if the product is discounted.                                                         |
| `basePrice`         | `string`   | ""        | Base price (e.g. price per liter) as label.                                                             |
| `weighing`          | `object`   | null      | Additional information for weighable products                                                           |

#### Code Object v1

**Deprecated**

| Name               | Type     | Default | Description                                                                                 |
|--------------------|----------|---------|---------------------------------------------------------------------------------------------|
| `code`             | `string` | ""      | Scannable code / barcode                                                                    |
| `transmissionCode` | `string` | null    | In case the POS cannot handle the scannable code / barcode, it contains a POS friendly code |

#### Weighing

**Deprecated**

Weighing attributes:

| Parameter         | Type       | Default | Description                                                                                               |
|-------------------|------------|---------|-----------------------------------------------------------------------------------------------------------|
| `weighedItemIds`  | `[]string` | null    | Templates for the scannable codes which encode price or weight information                                |
| `pluSet`          | `[]string` | null    | PLU, the short code to identify a weighable product                                                       |
| `weighByCustomer` | `bool`     | false   | Flag, if the product is prepackaged, or the customer has to do weighting by himself                       |
| `referenceUnit`   | `string`   | null    | The [unit](#supported-units) in which the price attribute is calculated (e.g. "kg" where price is EUR/Kg) |
| `encodingUnit`    | `string`   | null    | [Unit](#supported-units) which is used as encoding within the EAN (e.g. "g" when the EAN contains grams)  |

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

### Product List v2

*MIME type*: `application/vnd.snabble.product.list.v2+json`

Example:
```
{
   "products": [
      {
         "sku" : "1120325205",
         "name" : "Premium-Holzöl",
         // …
      }
   ]
}
```

| Parameter  | Type       | Description                                     |
|------------|------------|-------------------------------------------------|
| `products` | `[]object` | List of [Product Object v2](#product-object-v2) |

### Product List v1

**Deprecated**: Please, use the model v2.

*MIME type*: `application/vnd.snabble.product.list.v1+json`

Example:
```
{
   "products": [
      {
         "sku" : "1120325205",
         "name" : "Premium-Holzöl",
         // …
      }
   ]
}
```

| Parameter  | Type       | Description                                     |
|------------|------------|-------------------------------------------------|
| `products` | `[]object` | List of [Product Object v1](#product-object-v1) |

### Product JSON Stream v2

*MIME type*: `application/vnd.snabble.product.v2+x-ndjson`

[A newline delimited JSON stream](http://ndjson.org/) of [Product Object v2](#product-object-v2).

### Product JSON Stream v1

**Deprecated**: Please, use the model v2.

*MIME type*: `application/vnd.snabble.product.v1+x-ndjson`

[A newline delimited JSON stream](http://ndjson.org/) of [Product Object v1](#product-object-v1).

### Result Message

Result messages are simple status objects which are returned for batch operations.

| Parameter | Type     | Description                                               |
|-----------|----------|-----------------------------------------------------------|
| `sku`     | `string` | The product referenced by the result message              |
| `status`  | `string` | The processing status: "ok" or "error"                    |
| `message` | `string` | A human-readable message describing the processing status |

### Statistics Object

Statistics objects are a simple representation of the product statistics.

| Parameter | Type  | Description                              |
|-----------|-------|------------------------------------------|
| `count`   | `int` | The total product count                  |
| `created` | `int` | The amount of newly created products     |
| `updated` | `int` | The amount of updated products           |
| `deleted` | `int` | The amount of deleted products           |
| `inStock` | `int` | The amount of products that are in stock |


-----------

## Get product
`GET /{project}/products/sku/{sku}`

Return one product by sku.

**Required permissions** : productsRead

### Success Response `200 OK`

**Produces** :

* [application/vnd.snabble.product.v2+json](#product-object-v2)
* [application/json](#product-object-v1) (**Deprecated**)
* [application/vnd.snabble.product.v1+json](#product-object-v1) (**Deprecated**)

-----------

## Create product
`POST /{project}/products`

Create or update a product. If the product already exists, it will be updated.

**Required permissions** : productsWrite

### Request

**Consumes** :

* [application/vnd.snabble.product.v2+json](#product-object-v2)
* [application/json](#product-object-v1) (**Deprecated**)
* [application/vnd.snabble.product.v1+json](#product-object-v1) (**Deprecated**)

### Success Response `201 Created`

no content

-----------

## Update product
`PUT /{project}/products/sku/{sku}`

Create or update a product.
The sku in the url parameter and the JSON payload have to match.

**Required permissions** : productsWrite

### Request

**Consumes** :

* [application/vnd.snabble.product.v2+json](#product-object-v2)
* [application/json](#product-object-v1) (**Deprecated**)
* [application/vnd.snabble.product.v1+json](#product-object-v1) (**Deprecated**)

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

**Produces** :

* [application/vnd.snabble.product.v2+json](#product-object-v2)
* [application/json](#product-object-v1) (**Deprecated**)
* [application/vnd.snabble.product.v1+json](#product-object-v1) (**Deprecated**)

-----------

## Batch import

`POST /{project}/products/_batch`

Import a list of products in one HTTP request.

**Required permissions** : productsWrite

### Request

* [application/vnd.snabble.product.stream.v2+x-ndjson](#product-json-stream-v2)
* [application/x-ndjson](#product-json-stream-v1) (**Deprecated**)
* [application/vnd.snabble.product.stream.v1+x-ndjson](#product-json-stream-v1) (**Deprecated**)

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
| q | Passes a query string. Will do a full text search in the product name and description and will perform a prefix search with the product SKU, EAN and weighed item id. All products found by either search will be returned. |


### Success Response `200 OK`

**Produces** :

* [application/vnd.snabble.product.stream.v2+x-ndjson](#product-json-stream-v2)
* [application/x-ndjson](#product-json-stream-v1) (**Deprecated**)
* [application/vnd.snabble.product.stream.v1+x-ndjson](#product-json-stream-v1) (**Deprecated**)

-----------

## Delete products
`DELETE /{project}/products`

Deletes all products of a project.

**Required permissions** : productsWrite

### Request
**Example**:
`DELETE /example-project/products`

### Success Response `200 OK`

-----------

## Get statistics
`GET /{project}/products/statistics`

Return simple statistics about the products in a time period ranging from a selected date until now.

**Required permissions** : productsRead

### Request

**Parameters** :

| Name  | Description |
| ------------- | ------------- |
| since | The starting date of the time period. This parameter is required.|


### Success Response `200 OK`

**Produces** :

* [application/vnd.snabble.product.list.v2+json](#product-list-v2)
* [application/json](#product-list-v1) (**Deprecated**)
* [application/vnd.snabble.product.list.v1+json](#product-list-v1) (**Deprecated**)
