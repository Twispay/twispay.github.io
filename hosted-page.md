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

***Note!** Keep you `apiKey` private on your server. If it is exposed somebody can do malitions operations in your name.*


## How it works?

The Merchant places a “Purchase” button within an HTML form on their website. This submits a POST request to twispay® secure site.

For production, the secure URL is: https://secure.twispay.com    
For test, the secure URL is: https://secure-stage.twispay.com

In the POST Request parameters, the merchant specifies the location of the twispay® payment page, the payment details and authentication information. These are usually implemented as hidden input in the HTML form. 

The Mandatory Parameters are:

- Placeholder1
- Placeholder2
- Placeholder3
- Placeholder4
- Placeholder5


Here's a [Full List of Request Parameters](https://github.com/Twispay/twispay.github.io/blob/master/full-request-params.md) with description. 

After receiving and processing the request, Twispay sends back a response with useful data. This response is sent via a “server to server notification URL” and a “payment page back URL” provided by the Merchant, right after the payment, regardless of the result.

After the transaction is completed (either as successful or failed), the client can be redirected to a URL (payment page back URL) specified by the merchant.

The POST parameter result (result and opensslResult) sent via the “server to server notification URL” will contain the following keys: externalOrderId, identifier, status, customerId, orderId, cardId, transactionId, transactionKind, timestamp, amount, currency, custom and customField.

Note! The result parameter will be encrypted.

The response will have to be decrypted to make the data readable. 
Here's how you can do so [Link to Decryption Guide Page].

{
The following pages contain additional information about the platform and its features. Please take the time to read through them where necessary, as the above is only a basic guide to get things running. 

- Testing The Intergration
- Custom payment page specifications
- iFrame payment form
- Custom email receipt specifications
- Payout transactions for gambling (OCT/CFT)

}

OR

{ 

The Merchant can also: 
Additionally, the Merchant can
 
}  


