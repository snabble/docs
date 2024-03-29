> :warning: **Outdated document**: The snabble documentation has beeen moved to [docs.snabble.io](https://docs.snabble.io).
>
> :point_right: The current version of this document can befound at [docs.snabble.io/docs/api/API_CHANGELOG](https://docs.snabble.io/docs/api/API_CHANGELOG).


# API Changelog

All notable changes to the snabble API will be documented in this
file.

This file will be updated weekly, if there are notable changes.

## 2021-09-20

* Add `state`, `startedAt`, `cashRegisterID`, `sequenceNumber` and `externalCheckoutID` to [Order](api_orders.md#Order)

## 2021-05-20

* Deprecated authorization by `Client-Token` header in favor of Bearer Authorization

## 2021-04-16

* ADDED `isDiscountable` to [Product Object v2](api_products.md#product-object-v2)
* Clarified the usage of Price Modifiers in [VPoS API](api_vpos.md#discounts--modifiers)

## 2021-03-29

* ADDED Specification for Discounts and Modifiers in [VPoS API](api_vpos.md#discounts--modifiers)
* ADDED Specification for Coupons in [VPoS API](api_vpos.md#coupons)

## 2020-12-02

* Clarified the errors list in the [VPoS API](api_vpos.md#error)

## 2020-10-14

* ADDED `links.receipt` Documentation to [Order](api_orders.md#order)
* ADDED [`FiscalReference`](api_orders.md#fiscal-reference)  to [Order](api_orders.md#order)

## 2020-08-28

* ADDED `isPrimary` to [Code Object v2](api_products.md#code-object-v2)
* ADDED `specifiedQuantity` to [Code Object v2](api_products.md#code-object-v2)


## 2020-08-20

* ADDED maximal batch size of 20000 to [Products Batch Import](api_products.md#batch-import)
* ADDED maximal batch size of 20000 to [Batch Update Pricing](api_pricing.md#batch-update-pricing)
* ADDED maximal batch size of 20000 to [Batch Update Prices](api_pricing.md#batch-update-prices)

## 2020-07-29

* ADDED `priority`to [Pricing Category Object](api_pricing.md#pricing-category-object)

## 2020-07-20

* ADDED `notForSale` to [Product Object v2](api_products.md#product-object-v2)
* ADDED `DELETE /{project}/pricing/products`
* ADDED `DELETE /{project}/pricing/products/sku/{sku}/category/{categoryID}`

## 2020-07-17

* REMOVED documentation of [Product Object v1](api_products.md#product-object-v1) representation

## 2020-06-15

* REMOVED `checkoutProcess` property from `order` example

## 2020-03-18

* ADDED `clientID` and `appUserID` to [cart](api_checkout.md#cart), [checkoutInfo](api_checkout.md#checkout-info) and [checkoutProcess](api_checkout.md#checkout-process)

## 2020-03-05

* ADDED `mode` to [createClosingSchedule](api_orders.md#create-closing-schedule)

## 2020-01-23

* ADDED BASIC authentication scheme to the list of supported schemes for vPoS calls

## 2020-01-09

* ADDED `externalShopId` to [vPoS data model](api_vpos.md#data-model)

## 2019-12-10

* REMOVED closed field from [CheckoutProcess](api_checkout.md#checkout-process) and Close Request from [Modify Checkout Process](api_checkout.md#modify-checkout-process) Endpoint

## 2019-12-04
* ADDED [Closing](api_orders.md#get-closings) and [Create Closing Schedule](api_orders.md#create-schedule) endpoints

## 2019-09-09
* REMOVED `GET /{project}/orders/statistics`

## 2019-08-22
* REMOVED `GET /{project}/products/code/{code}`
* REMOVED `GET /{project}/products/weighItemId/{code}`
* REMOVED `GET /{project}/products/search/bySkus`
* REMOVED `GET /{project}/products/bundlesForSku/{bundledSku}`
* DEPRECATED [Get order statistics](#get-order-statistics): `GET /{project}/orders/statistics`

## 2019-07-30
* ADDED `customer.loyaltyCard` field to [order](api_orders.md#order)
* ADDED `session` field to [order](api_orders.md#order)

## 2019-07-25
* ADDED [endpoint to delete all products of a project](api_products.md#delete-produts)
* ADDED [endpoint to delete a pricing](api_pricing.md#delete-pricing)
* ADDED [batch endpoint for batch update
  pricings](api_pricing.md#batch-update-pricing) and clearified
  documentation

## 2019-05-29
* ADDED `customerCardPrice` to [`price`](#price-object) and
  `scanMessage` to [product v2
  object](api_products.md#product-object-v2)

## 2019-02-04

* ADDED Support for [units](api_products.md#supported-units) `cl`,
  `m2e-1`, `dm2`, `m2e-3`, `dm`, `dag` and `hg` in product API

## 2019-01-28

* ADDED [Product Object v2](api_products.md#product-object-v2) representation
* DEPRECATED [Product Object v1](api_products.md#product-object-v1) representation
* DEPRECATED `GET /{project}/products/code/{code}`
* DEPRECATED `GET /{project}/products/weighItemId/{code}`
* DEPRECATED `GET /{project}/products/search/bySkus`
* DEPRECATED `GET /{project}/products/bundlesForSku/{bundledSku}`

## 2019-01-10

* ADDED support for additional [units](api_products.md#supported-units)

## 2018-12-07

* DEPRECATED `eans` field in
  [products](api_products.md#product-object). It will be removed on
  February 1st 2019.
* REMOVED `boost` field in [products](api_products.md#product-object)

## 2018-11-20

* ADDED new [PoS Integration API](api_pos_integration.md)
* ADDED `currency` to Checkout Info and Order
* ADDED `paymentResult` to Checkout Process

## 2018-10-12

* ADDED new [Pricing API](api_pricing.md)

## 2018-10-11

* DEPRECATED `boost` field in [products](api_products.md#product-object). It will be removed after
  December 1st, 2018.
* FIXED documentation of the default values in
  [products](api_products.md#product-object). The now documented
  values conform to the behavior of the API.

## 2018-09-18

* ADDED `priceOrigin` to [line items](api_checkout.md#line-item)

## 2018-09-04

* ADDED [code object](api_products.md#code-object) to products
* ADDED clientID to orders in retailer portal

## 2018-07-23

* ADDED `fsk` sale restriction

## 2018-07-20

* ADDED barcodes to product detail view (retailer portal)
* CHANGED supervisor layout
* ADDED rescan to supervisor

## 2018-07-13

* ADDED `customersInShop` indicator to supervisor
* ADDED localization to supervisor

## 2018-07-06

### Product

* ADDED `bundledProduct` to model product bundles

## 2018-06-25

### Checkout

* ADDED `transferred` Payment State
* ADDED `createdAt` date to [Checkout Info](api_checkout.md#checkout-info)
* FIXED adding Line Items for deposits referenced by other Line Items

### Products

* REMOVED Deprecated `decimalDigits` and `currency` in
  [Product](api_products.md#product-object)
* FIXED several bugs in the appdb creation, which could
  lead to missing products.

## 2018-06-11

*Highlights:*

* ADDED New [Orders API](api_orders.md)
* ADDED support for arbitrary sku strings

*Deprecation:*

* DEPRECATED `decimalDigits` and `currency` in
  [Product](api_products.md#product-object)
  These will not be available anymore starting 22.06.2018

### Orders

* New [Orders API](api_orders.md)
* Show Orders in [Retailer Portal](https://retailer.snabble.io)

### Products

* ADDED support for arbitrary sku strings
* ADDED `controlIndication` and `forceControl` to the product. These
  parameters are used to parametrization the control indication
  algorithm
* ADDED `saleRestriction` to the product
* ADDED `saleStop` to the product
* FIXED: The type of the `sku` property of the [Result
  Message](api_products.md#result-message) now matches the documented
  one (`string`).
* FIXED: send the status code `404 Not Found` for a request to the
  appdb, if no products for the project are known.


### Checkout

* ADDED support for scannable codes that encode the number of units
  and the price. For this the [Line Item](api_checkout.md#line-item)
  was extended with the field `units` and `weight`
* ADDED the `scannedCode` to the [Line Item](api_checkout.md#line-item)
* Refuse to create a [Checkout Info](api_checkout.md#checkout-info)
  from [Carts](api_checkout.md#cart) that contain a product with a
  sale stop
