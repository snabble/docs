# API Changelog

All notable changes to the snabble API will be documented in this
file.

This file will be updated weekly, if there are notable changes.

## 2018-10-11

* DEPRECATED `boost` field in [products](api_products.md#product-object). It will be removed after
  December 1st, 2018.

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
