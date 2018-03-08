# Checkout with a POS

## Create a CheckoutInfo

To create a CheckoutInfo the Customer App sends a Cart and receives a
signed CheckoutInfo object. This object contains all information that
should be presented and confirmed by the user.

```
> echo '{
    "items": [
        {
            "sku": "1",
            "amount": 2
        }
    ],
    "shopID": ""
}' | http post https://api.snabble.io/test-project-faa116/checkout/info \
  "Client-Token:$(cat ~/.platform-test.jwt)"
HTTP/1.1 200 OK
Content-Encoding: gzip
Content-Length: 843
Content-Type: application/json
Date: Thu, 08 Mar 2018 16:45:33 GMT
Vary: Accept-Encoding
{
    "checkoutInfo": {
        "availableMethods": [
            "cash",
            "girocard",
            "sepa",
            "visa",
            "mastercard",
            "posQRCode"
        ],
        "lineItems": [
            {
                "amount": 2,
                "name": "Kugelschreiber tarent rot",
                "price": 399,
                "sku": "1",
                "taxRate": 19,
                "totalPrice": 798
            }
        ],
        "price": {
            "netPrice": 671,
            "price": 798,
            "tax": {
                "19": 127
            }
        },
        "project": "test-project-faa116",
        "session": "",
        "shopID": ""
    },
    "links": {
        "checkoutProcess": {
            "href": "/test-project-faa116/checkout/process"
        }
    },
    "signature": "..."
}
```

## Create a new CheckoutProcess

The customer app initializes a checkout process by sending the signed
CheckoutInfo and the selected payment method.

```
> echo '{
    "signedCheckoutInfo" : { --SNIP-- },
    "paymentMethod" : "posQRCode"
}' | \
  http post https://api.snabble.io/test-project-faa116/checkout/process \
  "Client-Token:$(cat ~/.app-platform-test.jwt)"
HTTP/1.1 201 Created
Content-Encoding: gzip
Content-Length: 452
Content-Type: application/json
Date: Thu, 08 Mar 2018 16:51:30 GMT
Location: /test-project-faa116/checkout/process/88bab157-7d5b-443d-8033-1c5c423bb97e
Vary: Accept-Encoding
{
    "aborted": false,
    "checkoutInfo": { --SNIP-- },
    "createdAt": "2018-03-08T16:51:30.279Z",
    "id": "88bab157-7d5b-443d-8033-1c5c423bb97e",
    "links": {
        "approval": {
            "href": "/test-project-faa116/checkout/process/88bab157-7d5b-443d-8033-1c5c423bb97e/approval"
        },
        "self": {
            "href": "/test-project-faa116/checkout/process/88bab157-7d5b-443d-8033-1c5c423bb97e"
        }
    },
    "modified": false,
    "paymentApproval": null,
    "paymentInformations": {
        "qrCodeContent": "sellfio:88bab157-7d5b-443d-8033-1c5c423bb97e"
    },
    "paymentMethod": "posQRCode",
    "paymentState": "pending",
    "pricing": {
        "lineItems": [
            {
                "amount": 2,
                "name": "Kugelschreiber tarent rot",
                "price": 399,
                "sku": "1",
                "taxRate": 19,
                "totalPrice": 798
            }
        ],
        "price": {
            "netPrice": 671,
            "price": 798,
            "tax": {
                "19": 127
            }
        }
    },
    "project": "test-project-faa116",
    "supervisorApproval": null
}
```

The service responds with the new CheckoutProcess. The document
contains additional information `paymentInformations`, that is used to
fulfill the workflow associated with the selected payment method. In
this case the content of the QR-Code presented to the POS. The format
of the `qrCodeContent` is `sellfio:<id of the checkout process>`. The
ID can be used to resolve the checkout process by replacing components
in the path `/<projectID>/checkout/process/<processID>` with it and
the ID of the project.

## Send new Pricing Information and mark the Payment as successful

The payment system should update the `paymentState` of the
CheckoutProcess and optionally provide a new Pricing Informations.

The possible states are `pending`, `successful` and `failed`. After
the `paymentState` of the process was set to a terminal state
(`successful` or `failed`) it is not possible to update the pricing.

The pricing can be incomplete. In this case order that gets generated
from such a CheckoutProcess is also incomplete.

```
> echo '{
   "paymentState" : "successful",
   "pricing" : {
      "lineItems" : [
         {
            "sku" : "1",
            "amount" : 1,
            "taxRate" : 19,
            "price" : 399,
            "totalPrice" : 399,
            "name" : "Kugelschreiber tarent rot"
         }
      ],
      "price" : {
         "price" : 399
      }
   }
}' | http patch https://api.snabble.io/test-project-faa116/checkout/process/88bab157-7d5b-443d-8033-1c5c423bb97e \
  "Client-Token:$(cat ~/.platform-test.jwt)"
HTTP/1.1 200 OK
Content-Encoding: gzip
Content-Length: 466
Content-Type: application/json
Date: Fri, 09 Mar 2018 14:27:08 GMT
Vary: Accept-Encoding

{
    "aborted": false,
    "checkoutInfo": { --SNIP-- },
    "createdAt": "2018-03-08T16:51:30.279Z",
    "id": "88bab157-7d5b-443d-8033-1c5c423bb97e",
    "links": {
        "approval": {
            "href": "/test-project-faa116/checkout/process/88bab157-7d5b-443d-8033-1c5c423bb97e/approval"
        },
        "self": {
            "href": "/test-project-faa116/checkout/process/88bab157-7d5b-443d-8033-1c5c423bb97e"
        }
    },
    "modified": false,
    "paymentApproval": null,
    "paymentInformations": {
        "qrCodeContent": "sellfio:88bab157-7d5b-443d-8033-1c5c423bb97e"
    },
    "paymentMethod": "posQRCode",
    "paymentState": "successful",
    "pricing": {
        "lineItems": [
            {
                "amount": 1,
                "name": "Kugelschreiber tarent rot",
                "price": 399,
                "sku": "1",
                "taxRate": 19,
                "totalPrice": 399
            }
        ],
        "price": {
            "price": 399
        }
    },
    "project": "test-project-faa116",
    "supervisorApproval": null
}
```
