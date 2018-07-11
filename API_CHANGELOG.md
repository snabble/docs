# API Changelog

All notable changes to the snabble API will be documented in this
file.

This file will be updated weekly, if there are notable changes.

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

* New [Orders API](api_orders.md)
* ADDED support for arbitrary sku strings

*Deprecation:*

* Deprecated `decimalDigits` and `currency` in
  [Product](api_products.md#product-object)
  These will not be available anymore starting 22.06.2018

### Orders

* New [Orders API](api_orders.md)
* Show Orders in [Retailer Portal](https://retailer.snabble.io)

### Products

* Added support for arbitrary sku strings
* Added `controlIndication` and `forceControl` to the product. These
  parameters are used to parametrization the control indication
  algorithm
* Added `saleRestriction` to the product
* Added `saleStop` to the product

* FIXED: The type of the `sku` property of the [Result
  Message](api_products.md#result-message) now matches the documented
  one (`string`).
* FIXED: send the status code `404 Not Found` for a request to the
  appdb, if no products for the project are known.


### Checkout

* Added support for scannable codes that encode the number of units
  and the price. For this the [Line Item](api_checkout.md#line-item)
  was extended with the field `units` and `weight`
* Added the `scannedCode` to the [Line Item](api_checkout.md#line-item)
* Refuse to create a [Checkout Info](api_checkout.md#checkout-info)
  from [Carts](api_checkout.md#cart) that contain a product with a
  sale stop
