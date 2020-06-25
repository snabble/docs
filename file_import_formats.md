# File Import Formats

The native way to get products into the snabble platform is by using the REST/JSON API.
It works out of the box and is the most powerful and flexible way. But of course, snabble also supports
the transfer by sFTP, using custom and project specific import formats, which are close to the following:

## SAP Retail: Assortment List

SAP Retail can export the Assortment List IDoc in XML format (WBBDLD). Snabble can handle this format, however, in contrast to the REST API and the CSV format,
it has the disadvantage that article data and prices are duplicated per store. This is very inefficient if the retailer actually has the same price in most stores.

## CSV Format

Using CSV, the following files have to be provided. It is possible to upload them in one tar.gz file:
* `products.csv`
* `codes.csv`
* `prices.csv` (optional)

The prices and discounted prices can be provided in two forms:
1. As columns in the `products.csv`
2.  In an additional `prices.csv`. This allows to assign an individual prices and discounts to a product in a pricing category. Pricing categories are mapped to shops. This allows to assign individual prices and discounts to shops. See the [Pricing API documentation](api_pricing.md) for details.

File format:
* encoding: `UTF-8`
* value separator: `,`


### products.csv

The `products.csv` contains one product per line. The [Products API documentation](api_products.md) explains the structure of a product.

The exact behavior of the importer and the set of columns in the CSV interface have to be determined in the on-boarding process.

Example CSV with prices:
```
#sku,name,taxCategory,depositProduct,bundledProduct,imageUrl,productType,price,discountedPrice
48,duplo white,normal,,,https://snabble.io/demodata/images/duplo-white-single.jpg,default,35,30
49,Deposit,normal,,,,deposit,8,
50,Deposit Six Pack,normal,,,,deposit,48,
51,Soda,normal,49,,,default,180,
52,Six Pack Soda,normal,50,51,,default,1080,1000
53,Apple,normal,,,,weighable,,
```

Example CSV without prices (`prices.csv` necessary):
```
#sku,name,taxCategory,depositProduct,bundledProduct,imageUrl,productType
48,duplo white,normal,,,https://snabble.io/demodata/images/duplo-white-single.jpg,default
49,Deposit,normal,,,,deposit
50,Deposit Six Pack,normal,,,,deposit
51,Soda,normal,49,,,default
52,Six Pack Soda,normal,50,51,,default
53,Apple,normal,,,,weighable
```

### codes.csv
The `codes.csv` contains the scannable EAN-8 or EAN-13 codes and refers to the product id.
In the [Products API documentation](api_products.md#code-object) you find more information on scannable codes.

Example CSV:
```
#code,sku,transmissionCode
42276630,48,
42123123,48,1231
46654261,51,
44444231,51,4442
33453234,52,
```

### prices.csv

The `prices.csv` contains prices which refer to a sku and a pricing category. For further information read the [Pricing API documentation](api_pricing.md)

Example:
```
#sku,pricingCategory,listPrice,discountedPrice
48,default,35,30
48,cheap,22,
51,default,180,
51,expensive,240,200
52,default,1080,1000
```

## Google shopping format
For simple scenarios, snabble supports the format which is used for the google shopping feeds with some minimal extensions.

Exmaple:
```
<?xml version="1.0" encoding="ISO-8859-15"?>
<feed xmlns="http://www.w3.org/2005/Atom" xmlns:g="http://base.google.com/ns/1.0">
  <entry>
    <g:id>1120325205</g:id>
    <g:gtin>0654203316514</g:gtin>
    <g:title>Premium-Holzöl</g:title>
    <g:image_link>https://www.example.com/some/image.jpg</g:image_link>
    <g:price>16.99</g:price>
    <g:sale_price>14.99</g:sale_price>
  </entry>
  ..
</feed>
```


