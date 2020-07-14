---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to VDT SMEmpower <i>simple</i> API! You can use our API to access VDT SMEmpower payment endpoints.

<aside class="success">
The <i>only</i> supported Content-Type for request and response to this API is JSON.
</aside>

Responses are returned using standard HTTP code syntax in header.

Code | Description
------- | -------
200	| Status okay.
400	| Bad input parameter -- The request is invalid.
401	| Bad or expired token. This can happen if the access token is expired or if the access token has been revoked.
403	| The request is not authorized or account doesn't have access to the endpoint.
405	| The request method is known by the server but is not supported by the target endpoint.
429 | Too Many Requests
5xx	| An error occurred on the servers. Please contact vdtcomms.com.

# Authentication

> Un-authenticated commands will return JSON structured like this:

```json

{
  message: "Unauthorized request."
}

```

VDT SMEmpower requires a <i>Bearer</i> Authorization API key to access to the API. Please contact itsupport@vdtcomms.com for more information. Your API key should be included in all API requests to the server in a header that looks like the following:

`Authorization: Bearer <your-api-key>`

<aside class="notice">
You must replace <code>&lt;your-api-key&gt;</code> with your personal API key.
</aside>

# Payments

##  Customer/Payment Initiation

```shell
curl "http://sme.vdtcomms.com/api/payment/initiate" \
  --header "Authorization: Bearer <your-api-key>" \
  --request POST \
  --data '{"referenceID":"<refID>","otherDetails":{"dataPlan":"<plan>"},"hash":"<hash>"}' \
```

> The above command returns JSON structured like this:

```json

{
    "referenceID": "123",
    "CustomerName": "Firstname Lastname",
    "statusCode": "00",
    "statusMessage": "Successful.",
    "otherDetails": {
        "dataPlan": "MARS_60GB",
        "description": "60GB + UNLIMITED NIGHT (MARS)"
    },
    "Currency": 566,
    "totalAmount": 13500,
    "hash": "bbb896575bdce239facaf44d1d42e4f5ee9c29c864abd183f6388f25082fc715ec2480c1059fe6f3c173e8316e3a5e62021d22577f39733d5500009b44643e7c"
}

```

This endpoint would be used to pull the details attached to a customer ID or payment reference for the customer to validate the provided information before payment processing is done.

### HTTP Request

`POST https://sme.vdtcomms.com/api/payment/initiate`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
referenceID | true | The customer ID.
otherDetails[dataPlan] | true | Data plan customer is purchasing.
hash | true | The SHA512 hash of all preceding parameters and the hash key.

## Payment Update Request

This endpoint would be used to update the payment details with status.

```shell
curl "http://sme.vdtcomms.com/api/payment/update" \
  --header "Authorization: Bearer <your-api-key>" \
  --request POST \
  --data '{"referenceID":"<id>","transReference":"<id>","totalAmount":"<amount>","Currency":"<id>","otherDetails":{"dataPlan":"<plan>"},"hash":"<hash>"}' \
```

> The above command returns JSON structured like this:

```json

{
    "referenceID": "123",
    "transReference": "transReference10101012",
    "paymentReference": "BANK_REFERENCE_101010",
    "responseCode": "01",
    "responseDesc": "Duplicate transaction detected on transReference.",
    "hash": "bbb896575bdce239facaf44d1d42e4f5ee9c29c864abd183f6388f25082fc715ec2480c1059fe6f3c173e8316e3a5e62021d22577f39733d5500009b44643e7c"
}

```

### HTTP Request

`POST https://sme.vdtcomms.com/api/payment/update`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
referenceID | true | The customer ID.
transReference | true | Bank's unique transaction reference generated for the payment
totalAmount | true | The total transaction amount.
Currency | true | The currency code of the transaction amount. This code must be in ISO numeric format.
otherDetails[dataPlan] | true | Data plan customer is purchasing.
hash | true | The SHA512 hash for request.


## Transaction Query

This endpoint should be called to query our platform for the status of a transaction using a unique reference number.

```shell
curl "http://sme.vdtcomms.com/api/payment/query" \
  --header "Authorization: Bearer <your-api-key>" \
  --request POST \
  --data '{"transReference":"<id>","hash":"<hash>"}' \
```

> The above command returns JSON structured like this:

```json

{
    "referenceID": "123",
    "transReference": "transReference10101012",
    "paymentReference": "BANK_REFERENCE_101010",
    "responseCode": "00",
    "responseDesc": "Successful.",
    "hash": "bbb896575bdce239facaf44d1d42e4f5ee9c29c864abd183f6388f25082fc715ec2480c1059fe6f3c173e8316e3a5e62021d22577f39733d5500009b44643e7c"
}

```

### HTTP Request

`POST https://sme.vdtcomms.com/api/payment/query`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
transReference | true | Bank's unique transaction reference generated for the payment
hash | true | The SHA512 hash for request.

