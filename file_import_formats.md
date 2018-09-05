
# File Import Formats

The native way to get products into the snabble platform is by using the REST/Json API.
It works out of the box and is the most powerfull and flexible way. But of course, snabble also supports
the transfer by sFTP, using custom and project specific import formats, which are close to the following:

## CSV Format

Using CSV, the following files have to be provided. They may be contained in one tar.gz archive:
* products.csv
* codes.csv

By default, the encoding should be UTF-8, and the default value separater is ",".

### products.csv

The `products.cvs` contains one line per product. For the meaning of the fields refer the API documentation in [Products Management API](api_products.md)
The exact behaviour of the importer and set of fields in the CSV interface have to be determined in the onboarding project.

Example:
```
sku,name,taxCategory,depositProduct,bundledProduct,imageUrl,productType,price,discountedPrice
1120325205,"Premium-Holzöl",19,,,"https://www.example.com/some/image.jpg","default",1699,1499
..
```

### codes.csv
The `codes.csv` contains the scannable EAN-8 or EAN-13 codes and refers to the product id.
For the meaning of the fields refer the API documentation in [Products Management API](api_products.md)

Example:
```
code,sku,transmissionCode
0654203316514,1120325205,
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


