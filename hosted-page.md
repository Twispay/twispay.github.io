# Hosted Payment Form Documentation

## Table of contents

[Introduction](#introduction)  
[Prerequisites](#prerequisites)  
[How it works?](#how-it-works)  


## Introduction

The Twispay® hosted payment page is a securely web form designed to accept e-commerce transactions.

With this page in place, customers are redirected from the merchant's website to a payment form hosted by Twispay®,
so the merchant is no longer exposed to the sensitive payment details (Card Number, CVV, etc.) required to process a payment.   

***Note!** If your site uses one of [supported platforms](#TODO) you can skip this giude
and go directly to [modules documentation](#TODO).*


## Prerequisites
- This guide.
- An e-commerce site.
- A Twispay® merchant account. If you don't already have one you can create one [here](https://merchant-stage.twispay.com/auth/signup).
- A `siteId` and secret key (aka `apiKey`) you can get from your Twispay® merchant account. See below where to find it.

![](siteID&apiKey.gif)

***Note!** Keep your `apiKey` private on your server. If it is exposed somebody can do malitions operations in your name.*


## How it works?

The Merchant submits a POST request to twispay® secure site with the necessary parameters and receives a POST Response back.
The process can be broken up into three simples steps:

Prepare the checkout - Create the Payment Form - Receive the Payment Status


### Prepare the Checkout

The first step is to collect the required data from the customer, to be sent in the payment POST Request. The minimum mandatory parameters needed for the request are: siteId, amount, currency, orderType, cardTransactionMode, orderId, identifier, Description.

Depending on the Bank you have a contract with you might have to provide additional parameters (email, address, country, city, state, etc).

Here you can find a [Full List of Request Parameters](https://github.com/Twispay/twispay.github.io/blob/master/full-request-params.md) with descriptions. 


### Create the Payment Form

Next you need to create a Payment Form to Submit the Post Request. 

In the POST Request parameters, the merchant specifies the location of the twispay® payment page, the payment details and authentication information. These are usually implemented as hidden input in the HTML form. 

For production, the secure URL is: https://secure.twispay.com    
For test, the secure URL is: https://secure-stage.twispay.com

Here's an example of sample code used for generating a HTML form for a Twispay Order: 

```Java
// get the HTML form
String base64JsonRequest = Twispay.getBase64JsonRequest(jsonOrderData);
String base64Checksum = Twispay.getBase64Checksum(jsonOrderData, secretKey.getBytes(StandardCharsets.UTF_8));
String hostName = twispayLive ? "secure.twispay.com" : "secure-stage.twispay.com";
String htmlForm = "<form action=\"https://" + hostName + "\" method=\"post\" accept-charset=\"UTF-8\">\n"
    + "<input type=\"hidden\" name=\"jsonRequest\" value=\"" + base64JsonRequest + "\">\n"
    + "<input type=\"hidden\" name=\"checksum\" value=\"" + base64Checksum + "\">\n"
    + "<input type=\"submit\" value=\"Pay\">\n"
    + "</form>";
```

### Receive the Payment Status

After processing the request, Twispay sends back a response with data via a “server to server notification URL” and a “payment page back URL” provided by the Merchant, right after the payment, regardless of the result.

After the transaction is completed (either as successful or failed), the client can be redirected to a URL (payment page back URL) specified by the merchant.

The POST parameter result (result and opensslResult) sent via the “server to server notification URL” will contain the following keys: externalOrderId, identifier, status, customerId, orderId, cardId, transactionId, transactionKind, timestamp, amount, currency, custom and customField.

The result parameter will be encrypted, to it will have to be decrypted to make the data readable. 
Here's how you can do so [Link to Decryption Guide Page].

See Also:

- Testing The Intergration
- Custom payment page specifications
- iFrame payment form
- Custom email receipt specifications
- Payout transactions for gambling (OCT/CFT)
