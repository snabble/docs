
# QR code formats

Snabble supports online payments as well as a transmission of a shopping cart to the retailers cash desk.
For the transmission, the following QR code formats are supported out of the box.

## QrCodePOS

With this format, the QR code just contains the ID of the checkout process.
The cash register is then able to fetch the shopping cart from the snabble POS service, using this id.

## Scanned codes with quantities

All scanned codes are written into one QR code in a CSV-style (semicolon-separated) format. Each line consists of a quantity and the scanned code of the product, and a header line allows easy detection of this format. For example, one *Duplo (40084015)* and two glasses of *Nutella (4008400401621)* would be encoded as:

````
snabble;
1;40084015
2;4008400401621
````

Lines are separated by a single newline character `\n`. The first line always contains the character sequence `snabble;`.

![QR code encoded codes with XEXEXZ](img/qr-code-encoded-codes-quantity.png)

For each project on the snabble platform, a different set of delimiters and other parameters for the QR code can be configured. The available formatting parameters for this encoding are:

| Name      | Default         | Description                                           |
|-----------|-----------------|-------------------------------------------------------|
| maxCodes  | 100             | Maximun number of lines to fit into a single QR code. |

## Repetitions of scanned codes

With this encoding, all scanned codes are written into one QR code as if they were scanned one-by-one. By default, the scanned codes are separated by a newline character, so the code contains the codes line by line.

Example: One *Duplo (40084015)* and two glasses of *Nutella (4008400401621)*.

With default formating:

```
0000040084015
4008400401621
4008400401621
```

![QR code encoded codes with newlines](img/qr-code-encoded-codes_newline.png)

For each project on the snabble platform, a different set of delimiters and other parameters for the QR code can be configured. The available formatting parameters for this encoding are:

| Name      | Default         | Description                                           |
|-----------|-----------------|-------------------------------------------------------|
| prefix    | ""              | The string at the beginning of the QR code.           |
| separator | "\n"            | The separator string between two scanned codes.       |
| suffix    | ""              | A string at the end of the QR code.                   |
| maxCodes  | 100             | Maximun number of codes to fit into a single QR code. |

The above example with prefix and delimiter: "XE" and suffix: "XZ" will result in the following code:

```
XE0000040084015XE4008400401621XE4008400401621XZ
```

![QR code encoded codes with XEXEXZ](img/qr-code-encoded-codes_XEXEXZ.png)


