# Hosted Payment Form Documentation

## Table of contents

[Introduction](#introduction)  
[Prerequisites](#prerequisites)  
[How it works?](#how-it-works)  


## Introduction

The Twispay® hosted payment page is a securely web form designed to accept e-commerce transactions.

With this page in place, customers are redirected from the merchant's website to a payment form hosted by Twispay®,
so the merchant is no longer exposed to the sensitive payment details (Card Number, CVV, etc.) required to process a payment.   

***Note!** If your site uses one of [supported platforms](#TODO) you can skip this guide
and go directly to [modules documentation](#TODO).*


## Prerequisites
- This guide.
- An e-commerce site.
- A Twispay® merchant account. If you don't already have one you can create one [here](https://merchant-stage.twispay.com/auth/signup).
- A `siteId` and secret key (aka `apiKey`) you can get from your Twispay® merchant account. See below where to find it.

![](siteID&apiKey.gif)

***Note!** Keep your `apiKey` private on your server. If it is exposed somebody can do malitions operations in your name.*


## How it works?

The Payment process is initiated through a HTTP POST Request to the hosted page.  

1. Prepare the checkout &#8594; 2. Create the Payment Form &#8594; 3. Receive the Payment Status


### 1. Prepare the Checkout

First, collect the required data from the customer.
The minimum mandatory parameters needed for the request are: siteId, amount, currency, orderType, cardTransactionMode, orderId, identifier, Description.

Depending on your contracted bank, you might have to include additional parameters (email, address, country, city, state, etc).

A [Full List of Request Parameters](full-request-params.md) with descriptions is available for more details. 


### 2. Create the Payment Form

On your back-end server, first build the requested JSON and sign it so that the customer cannot change your data. 

```javascript
let orderData = {
    "siteId": <mySiteId>,
    "customer": {
        "identifier": "<myUserId>",
        "email": "<customerEmailAddress>"
    },
    "order": {
        "orderId": "<myOrderId>",
        "type": "purchase",
        "amount": 100.00,
        "currency": "EUR",
        "description": "<myPaymentDescription>"
    },
    "cardTransactionMode": "authAndCapture",
    "backUrl": "https://<myDomain>/<pathToThankYouPage>"
}

const secretKey = "<myTwispayApiKey>",
      twispayLive = false,
      hostName = twispayLive ? "secure.twispay.com" : "secure-stage.twispay.com";

const base64JsonRequest = twispay.getBase64JsonRequest(orderData),
      base64Checksum = twispay.getBase64Checksum(orderData, secretKey);
```
***Note!** The Secret Key must remain secret, this is why you should never run this code client-side.*  
***Note!** Previous example code is taken from NodeJS SDK.*  

Then, on your front-end, display the HTML Form and auto-submit it using Javascript. 

```HTML
<form action="https://${hostName}" method="post" accept-charset="UTF-8" id="myForm">
    <input type="hidden" name="jsonRequest" value="${base64JsonRequest}">
    <input type="hidden" name="checksum" value="${base64Checksum}">
    <input type="submit" value="Pay">
</form>

<script type="javascript">
    $(document).ready(function() {
        $("#myForm").submit();
    });
</script>
```
***Note!** For simplicity, we used JQuery Framework code for auto-submitting the form.*

We also provide SDKs for:
<a href="https://github.com/Twispay/hostedpage-php-sdk" target="_blank">PHP</a>,
<a href="https://github.com/Twispay/hostedpage-dotnet-sdk" target="_blank">DotNet</a>,
<a href="https://github.com/Twispay/hostedpage-nodejs-sdk" target="_blank">NodeJS</a>,
<a href="https://github.com/Twispay/hostedpage-python-sdk" target="_blank">Python</a>,
<a href="https://github.com/Twispay/hostedpage-java-sdk" target="_blank">Java</a>.

If you're not using any of these languages, you can build your own [checksum generator](#TODO).

### 3. Receive the Payment Status

In this step, there are two URLs you need to provide. 
After a transaction is completed, the customer is redirected to a Payment Page Back URL. 
The request response sent by Twispay needs a landing location in the form of a "Server to Server Notification URL".  
These can be set via <>. 

The POST parameter result (result and opensslResult) sent via the “server to server notification URL” contain the following keys: externalOrderId, identifier, status, customerId, orderId, cardId, transactionId, transactionKind, timestamp, amount, currency, custom and customField.

The result parameter will be encrypted, so you will have to decrypt it to make the data readable. 
Here's how you can do so [[Link to Decryption Guide Page](https://github.com/Twispay/twispay.github.io/blob/master/Response%20Decryption%20Guide)].

See Also:

- [Testing The Intergration](https://github.com/Twispay/twispay.github.io/blob/master/integration-tests.md)
- Custom payment page specifications
- iFrame payment form
- Custom email receipt specifications
- Payout transactions for gambling (OCT/CFT)
