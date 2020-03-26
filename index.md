#  **Twispay® Hosted Payment Page**



## Table of Contents  

1. Introduction  
2. The Request   
3. Request Parameters  
4. Decrypting the Response  
5. Testing the Integration  
6. Custom Payment Page Specifications  
7. iFrame Payment Form  
8. Payout Transactions for Gambling  


# Introduction  

This document is intended for Merchants / Web Developers who want to understand and integrate Twispay's payment process features with a merchant's website.   

The Twispay® payment page is a securely hosted web payment form designed to accept internet e-commerce transactions.  

With Twispay's payment page in place, customers are redirected from the merchant's website to a payment form hosted by Twispay, so the merchant is no longer exposed to the sensitive payment details (Card Number, CVV, etc.) required to process a payment.   

To accomplish this, the merchant displays a "Purchase" button within an HTML form on their website that will submit a POST request to the twispay® secure site.  

- For production, the secure URL is: https://secure.twispay.com
- For test, the secure URL is: https://secure-stage.twispay.com

Within the parameters of this HTML form, the merchant specifies the location of the twispay® payment page along with payment details and authentication information. These are usually implemented as hidden input in the HTML form.
At the end of the payment process all parameters that define the status of the transaction can be returned to the merchant in real-time at a provided URL (server-to-server notification URL).

Once you have your Merchant  account, you will be given a siteId and a secret unique key (aka apiKey). This key is used to authenticate and communicate with our servers securely. The apiKey can be found in the merchant account (https://merchant.twispay.com or for test accounts https://merchant-stage.twispay.com), under the “Sites” menu – clicking the number found at column # will bring up a site details window.

The apiKey is listed in the site details view as “Private key”

Note! The apiKey is unique for each website – if you had a test account, the apiKey used for tests is not the same as the one you will need to use for the live environment.

<img src="./image.jpg" />

[ Markdown Doesn't have Image Support]

# Request parameters



These are the parameters that can be included in your Secure request. 



| **Name**                                       |                      **Type / Length**                       | **Mandatory** |
| :--------------------------------------------- | :----------------------------------------------------------: | ------------- |
| Authentication parameters**                    |                                                              |               |
| siteId                                         |                integer – provided by twispay                 | yes           |
| checksum                                       | string – see chapter [Request   signature](#_Request_signature) | yes           |
| threeDSecureData                               | Base64Encoded 3DSecure 2.0 Json (structure explained bellow  under 3DSecure 2.0 section) | no            |
| **Customer details parameters**                |                                                              |               |
| customer.identifier                            |  string – customer ID assigned by merchant/ char limit - 92  | yes           |
| customer.firstName                             |                            string                            | yes           |
| customer.lastName                              |                            string                            | yes           |
| customer.country                               | string - Use [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) codes. Eg. US | yes           |
| customer.state                                 | string – mandatory for US and CA; use **2 letters** [ISO 3166-2:US](https://en.wikipedia.org/wiki/ISO_3166-2:US) for US and [ISO 3166-2:CA](https://en.wikipedia.org/wiki/ISO_3166-2:CA) for CA. Eg. NY | conditional   |
| customer.city                                  |                            string                            | yes           |
| customer.zipCode                               |                  string – no spaces allowed                  | no            |
| customer.address                               |                            string                            | no            |
| customer.phone                                 |                  string – no spaces allowed                  | yes           |
| customer.email                                 |                            string                            | yes           |
| customer.tags                                  |                indexed array of string values                | no            |
| **Transaction details parameters**             |                                                              |               |
| order.amount                                   |             float – use dot as decimal separator             | yes           |
| order.currency                                 | string – use [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217) three letter code | yes           |
| order.description                              | string – 77056 characters long; mandatory if item is not defined | conditional   |
| order.orderType                                | string – one of: “purchase”, “recurring”,  “managed”, “credit” | yes           |
| order.orderId                                  | string – your identifier of the transaction; must be unique for  each request | yes           |
| order.retryPayment                             | string – Comma separated values  representing retry intervals for failed re-bill payments, use [ISO 8601 Durations](https://en.wikipedia.org/wiki/ISO_8601#Durations) [ex: P1D,P2D,P3D] | no            |
| order.tags                                     |                indexed array of string values                | no            |
| cardTransactionMode                            | string – one of: “auth” (only reserve the requested  amount), “authAndCapture” (also sends a settlement request), “credit” | yes           |
| backUrl                                        | string – used to redirect the customers back to the merchant’s  site; we also post an encrypted transaction result to this URL | yes           |
| cardId                                         |  integer – id of a previously used card for this  customer   | no            |
| order.intervalType                             |               string – one of: “day”, “month”                | conditional   |
| order.intervalValue                            | integer – recurring interval value; Eg. Set  “intervalValue” = 1 and “intervalType” = month; for a monthly subscription  plan | conditional   |
| order.trialAmount                              |   float – use dot as decimal separator; cannot be 0 (zero)   | conditional   |
| order.firstBillDate                            | string – use [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) date, time and timezone offset;  declare the date of the first recurring payment | conditional   |
| invoiceEmail                                   |   string – an alternative email address to send invoice to   | no            |
| **Cart/products details parameters**           |                                                              |               |
| order.items                                    | indexed array of cart items / products , the item structure is  described bellow on „Cart items” section | no            |
| **Custom parameters**                          |                                                              |               |
| custom                                         |              associative array of string values              | no            |
| **Level 3 Data additional request parameters** |                                                              |               |
| order.level3Type                               |            string – value: “airline”,  “tourism”             | conditional   |
| order.level3Airline.ticketNumber               |                     string – varchar 64                      | conditional   |
| order.level3Airline.passengerName              |                     string – varchar 512                     | conditional   |
| order.level3Airline.flightNumber               |                     string – varchar 64                      | conditional   |
| order.level3Airline.departureDate              | datetime  [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) Eg.  2018-02-05T14:13:33+00:00  yyyy-mm-ddThh:mm:ss+00:00  UTC always | conditional   |
| order.level3Airline.departureAirportCode       |                      string – varchar 3                      | conditional   |
| order.level3Airline.arrivalAirportCode         |                     string  – varchar 3                      | conditional   |
| order.level3Airline.carrierCode                |                     string – varchar 32                      | conditional   |
| order.level3Airline.travelAgencyCode           |                     string  – varchar 32                     | conditional   |
| order.level3Airline.travelAgencyName           |                    string – varchar  512                     | conditional   |
| order.level3Airline.level3TourismTourNumber    |                     string  – varchar 64                     | conditional   |
| order.level3Tourism.travellerName              |                    string – varchar  512                     | conditional   |
| order.level3Tourism.departureDate              | datetime  – [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) as  above | conditional   |
| order.level3Tourism.returnDate                 | datetime – [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) as  above | conditional   |
| order.level3Tourism.agencyCode                 |                     string  – varchar 32                     | conditional   |
| order.level3Tourism.agencyName                 |                    string – varchar  512                     | conditional   |

# Cart Items Data

Bellow is explained the structure of an item array present in the „items” array. 

Note! Please consider that the sum of the items amounts must match the amount of the transaction.

 

| **Name**                       | **Type**  | **Details**                                                  |
| ------------------------------ | --------- | ------------------------------------------------------------ |
| order.items[0].item            | `string`  | name of the product                                          |
| order.items[0].unitPrice       | `string`  | float (can have negative value  for discounts, vouchers, etc.) – including VAT |
| order.items[0].units           | `integer` | quantity                                                     |
| order.items[0].type            | string    | `string – one of: “physical”, “digital”`                     |
| order.items[0].code            | string    | `code of the item => string - varchar 64`                    |
| order.items[0].vatPercent      | float     | `VAT percent`                                                |
| order.items[0].itemDescription | string    | `description => string - varchar 500`                        |

 

Note! You can validate your JSON using any JSON validator and the Twispay validation schema, which you can find here: https://secure.twispay.com/schema/v1/order.schema.json.

Note! For Level 3 Data, item information (Cart/products details parameters) becomes mandatory besides the Level 3 Data additional request parameters listed in the table.

Note! The ‘orderId’ must be unique for each request since it is stored in the customer’s cookies and re-attempting to make a payment with the same ID will immediately return the same result as the initial submit.

Ex: if the first attempt failed, re-submitting with the same ID will take the customer directly onto the page where the fail message is displayed.

# 3-D Secure 2.0

 

In order to manage fraud while maintaining a positive consumer experience 3-D Secure 2.0 uses contextual data sharing between merchant and issuer. The new 3-D Secure 2.0 standard makes it possible to have significantly higher amounts of data between the merchant and the bank, which adds an additional layer of security and streamlines the checkout process. The data can be used by the bank to assess the risk of a transaction as part of its anti-fraud detection system.

 

In some instances, such as a cardholder travelling or making an unusual purchase, an issuer may suspect a  transaction to be suspicious; if some of the data elements match but other don’t the issuer may or may not ask the cardholder for additional info to verify their identity. 

 

As a merchant you can collect and pass this data to the payment gateway (Twispay®) when submitting the payment request.  You must add a new parameter to your request called ***“threeDSecureData”.\*** Inside this parameter you have to add the following data as Base64 encoded JSON.



| **Name**                 | **Type**  | **Details**                                                  |
| ------------------------ | --------- | ------------------------------------------------------------ |
| browserAcceptHeader      | `string`  | Between 2 and 2048 char; Exact content of the HTTP  accept headers as sent to the 3DS Requestor from the Cardholder’s browser |
| browserIP                | `string`  | Format ipv4 or ipv6; IP address of the  browser as returned by the HTTP headers to the 3DS Requestor |
| browserJavaEnabled       | `boolean` | Represents  the ability of the cardholder browser to execute Java. Value is returned from  the navigator.javaEnabled property |
| browserJavaScriptEnabled | boolean   | `Boolean that represents the ability of the cardholder browser to execute JavaScript` |
| browserLanguage          | string    | `Between 1 and 8 char; Value representing the browser language as defined in IETF BCP47. Returned from navigator.language property` |
| browserColorDepth        | string    | `Obtained from Cardholder browser using the screen.colorDepth property` |
| browserScreenHeight      | string    | `Value is returned from the screen.height property; ``pattern: "^[1-9][0-9]{0,5}$"` |
| browserScreenWidth       | string    | `Value is returned from the screen.width property; `  pattern: "^[1-9][0-9]{0,5}$" |
| browserTZ                | string    | `Time-zone offset in minutes between UTC and the Cardholder browser local time. Note that the offset is positive if the local time zone is behind UTC and negative if it is ahead; ``"pattern": "^[\\+\\-]?[0-9]{1,5}$"` |
| browserUserAgent         | string    | Between 2 and 2048 char                                      |
| cardholderName           | string    | Between 2 and 45 char                                        |
| Email                    | string    | Max length: 254 char; format email                           |
| homePhone                | object    | `“cc” (Country code); type: string; pattern: "^[0-9]{1,3}$"`  “subscriber” (Phone number); type: string; max  length: 15 char |
| mobilePhone              | object    | `“cc” (Country code); type: string; pattern: "^[0-9]{1,3}$"`  “subscriber” (Phone number); type: string; max length: 15 char |
| workPhone                | object    | `“cc” (Country code); type: string; pattern: "^[0-9]{1,3}$"`  “subscriber” (Phone number); type: string; max  length: 15 char |
| billAddrCity             | string    | Between 1 and 50 char                                        |
| billAddrCountry          | string    | `ISO 3166-1 numeric three-digit country code, other than exceptions listed in Table A.5; pattern: "^[0-9]{3}$"` |
| billAddrLine1            | string    | Between 1 and 50 char                                        |
| billAddrLine2            | string    | Between 1 and 50 char                                        |
| billAddrLine3            | string    | Between 1 and 50 char                                        |
| billAddrPostCode         | string    | Max length: 16 char                                          |
| billAddrState            | string    | Max length: 3 char; Should be the  country subdivision code defined in ISO 3166-2 |
| shipAddrCity             | string    | Between 1 and 50 char                                        |
| shipAddrCountry          | string    | ISO 3166-1 numeric three-digit country code, other  than exceptions listed in Table A.5; pattern: "^[0-9]{3}$" |
| shipAddrLine1            | string    | Between 1 and 50 char                                        |
| shipAddrLine2            | string    | Between 1 and 50 char                                        |
| shipAddrLine3            | string    | Between 1 and 50 char                                        |
| shipAddrPostCode         | string    | Between 1 and 16 char                                        |
| shipAddrState            | string    | `Between 1 and 3 char; Should be the country subdivision code defined in ISO 3166-2` |
| addrMatch                | string    | `Enum: “Y” or “N”; Indicates whether the Cardholder Shipping Address and Cardholder Billing Address are the same` |

 



By default, Twispay® will collect the following data from the card holders: “**browserAcceptHeader**”, “**browserIP**”, “**browserJavaScriptEnabled**”, “**browserUserAgent**”, “**browserLanguage**”; also if the card holder has “ **browserJavaScriptEnabled**” = true, then we will also get the following information: “**browserColorDepth**”, “**browserScreenHeight**”, “**browserScreenWidth**” and “**browserTZ**”.

 Sending this data inside the ***threeDSecureData\*** parameter will override the information collected by us.

Note! You can validate your JSON using any validator and the Twispay validation schema, which you can find here: https://secure-stage.twispay.com/schema/3ds/3ds.schema.json 



Example of JSON containing 3D Secure 2.0 data 
(please consider that this JSON must be base64encoded in the threeDSecureData param)

```json
{
  "Email": "john.doe@test.com",
  "addrMatch": "Y",
  "billAddrCity": "New York",
  "billAddrCountry": "840",
  "billAddrLine1": "Madison Ave 104th",
  "billAddrLine2": "Building A",
  "billAddrLine3": "Apt 123",
  "billAddrPostCode": "10001",
  "billAddrState": "NY",
  "browserJavaScriptEnabled": false,
  "browserJavaEnabled": false,
  "browserAcceptHeader": "application/json",
  "browserColorDepth": "16",
  "browserIP": "172.16.254.1",
  "browserLanguage": "en",
  "browserScreenHeight": "920",
  "browserScreenWidth": "1280",
  "browserTZ": "-300",
  "browserUserAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.90 Safari/537.36",
  "cardholderName": "John Doe",
  "homePhone": {
    "cc": "1",
    "subscriber": "2125096995"
  },
  "mobilePhone": {
    "cc": "1",
    "subscriber": "2345187886"
  },
  "workPhone": {
    "cc": "1",
    "subscriber": "2212366542"
  },
  "shipAddrCity": "New York",
  "shipAddrCountry": "840",
  "shipAddrLine1": "Madison Ave 104th",
  "shipAddrLine2": "Building A",
  "shipAddrLine3": "Apt 123",
  "shipAddrPostCode": "10001",
  "shipAddrState": "NY"
}
```



 **Building the request**

**Example of a simple request using PHP**

```php
$orderData = [
    "siteId" => 1,
    "customer" => [
        "identifier" => "123456",
        "firstName" => "John ",
        "lastName" => "Doe",
        "country" => "US",
        "state" => "NY",
        "city" => "New York",
        "address" => "1st Street",
        "zipCode" => "11222",
        "phone" => "+402120000000",
        "email" => "john.doe@test.com"
    ],
    "order" => [
        "orderId" => "ord123456",
        "type" => "purchase",
        "amount" => 2194.99,
        "currency" => "USD",
        "description" => "product description"
    ],
    "cardTransactionMode" => "authAndCapture",
    "invoiceEmail" => "john.doe@test.com",
    "backUrl" => "http://example.com"
];

$secretKey = "cd07b3c95dc9a0c8e9318b29bdc13b03";

$twispayLive = false;

$base64JsonRequest = Twispay::getBase64JsonRequest($orderData);
$base64Checksum = Twispay::getBase64Checksum($orderData, $secretKey);
$hostName = $twispayLive ? "secure.twispay.com" : "secure-stage.twispay.com";
$htmlForm = <<<FORM
<form action="https://{$hostName}" method="post" accept-charset="UTF-8">
    <input type="hidden" name="jsonRequest" value="{$base64JsonRequest}">
    <input type="hidden" name="checksum" value="{$base64Checksum}">
    <input type="submit" value="Pay">
</form>
FORM;

```

**Example of a complete JSON order data**

```json
{
  "siteId": 1,
  
//3d secure data
  
  "customer": {
    "identifier": "identifier",
    "firstName": "John ",
    "lastName": "Doe",
    "country": "US",
    "state": "NY",
    "city": "New York",
    "address": "1st Street",
    "zipCode": "11222",
    "phone": "0012120000000",
    "email": "john.doe@test.com",
    "tags": [
      "customer_tag_1",
      "customer_tag_2"
    ]
  },
  "order": {
    "orderId": "external-order-id",
    "type": "recurring",
    "amount": 2194.98,
    "currency": "USD",
    "description": "order description",
    "retryPayment": "P1D,P2D,P3D",
    "items": [
      {
        "item": "1 year subscription on site",
        "unitPrice": 34.99,
        "units": 1,
        "type": "digital",
        "code": "xyz",
        "vatPercent": 19,
        "itemDescription": "1 year subscription on site"
      },
      {
        "item": "Virtual Book#1",
        "unitPrice": 10.75,
        "units": 200,
        "type": "digital",
        "code": "abc",
        "vatPercent": 19,
        "itemDescription": "Ebook"
      },
      {
        "item": "discount",
        "unitPrice": -10,
        "units": 1,
        "type": "digital",
        "code": "fgh",
        "vatPercent": 0,
        "itemDescription": "discount"
      }
    ],
    "tags": [
      "tag_1",
      "tag_2"
    ],
    "intervalType": "month",
    "intervalValue": 1,
    "trialAmount": 1,
    "firstBillDate": "2018-12-02T12:00:00+00:00",
    "level3Type": "airline",
    "level3Airline": {
      "ticketNumber": "8V32EU",
      "passengerName": "John Doe",
      "flightNumber": "SQ619",
      "departureDate": "2019-02-05T14:13:00+02:00",
      "departureAirportCode": "KIX",
      "arrivalAirportCode": "OTP",
      "carrierCode": "American Airlines",
      "travelAgencyCode": "19NOV05",
      "travelAgencyName": "Elite Travel"
    }
  },
  "cardTransactionMode": "authAndCapture",
  "cardId": 1,
  "invoiceEmail": "john.doe@test.com",
  "backUrl": "http://example.com",
  "customData": {
    "key1": "value",
    "key2": "value"
  }
}


```

JSON encode the order data from above and then use base64JsonRequest and base64Checksum (using the Twispay API Key) to generate an HTML form. 



```php
class Twispay
{
    public static function getBase64JsonRequest(array $orderData)
    {
        return base64_encode(json_encode($orderData));
    }

    public static function getBase64Checksum(array $orderData, $secretKey)
    {
        $hmacSha512 = hash_hmac('sha512', json_encode($orderData), $secretKey, true);
        return base64_encode($hmacSha512);
    }
} 

```

Depending on the environment submit this form to either our test URL (https://secure-stage.twispay.com/) or the the production URL (https://secure.twispay.com/).



```html
$htmlForm = <<<FORM
<form action="https://secure-stage.twispay.com" method="post" accept-charset="UTF-8">
    <input type="hidden" name="jsonRequest" value="{$base64JsonRequest}">
    <input type="hidden" name="checksum" value="{$base64Checksum}">
    <input type="submit" value="Pay">
</form>
FORM;

```



Note! You can find examples of requests online on: https://github.com/Twispay

# Response parameters

You will have to provide us with a “server to server notification URL” and a “payment page back URL”, where you will receive the response from twispay®. A response will be sent on both channels right after the payment regardless of the result:

 

1.First on the browser redirect to your “payment page back URL”, immediately after the payment;

2.Second, on the “server to server notification URL”; this is useful for those cases when the browser fails and we need to notify you on the status of a transaction. The IPN (Instant Payment Notification) that we send to the “server to server notification URL” may have delays which is why you should use the result received at the “payment page back URL” to update your transaction status and post the correct message to your customers. To acknowledge that you received the IPN, your server needs to respond with 200 OK, with the text OK being the only content in the page. In case your server does not respond when we send you the IPN we have a retry scheme (1 min, 5 min, 1h, 24h) before dropping the message.

The POST parameter result (`result` and `opensslResult`) sent via the “server to server notification URL” will contain the following keys: `externalOrderId`, `identifier`, `status`, `customerId`, `orderId`, `cardId`, `transactionId`, `transactionKind`, `timestamp`, `amount`, `currency`, `custom` and `customField`

Note! `result` is only used for backwards compatibility and we recommend using only `opensslResult`

Note! When you retrieve data from the api [ORDER ID GET] the orderId parameter is the external order ID ]

Note! The result parameter will be encrypted.

Here is an example of an `opensslResult` (after being decrypted):



```php
Array(
    [status] => complete-ok
    [externalOrderId] => external-order-id
    [identifier] => identifier
    [customerId] => 1
    [orderId] => 123
    [cardId] => 3
    [transactionId] => 1234
    [transactionKind] => card
    [custom] => Array(
        [key_1] => value_1
        [key_2] => value_2
    )
    [customField] => Array()
    [amount] => 45
    [currency] => EUR
    [timestamp] => 1485939750
)

```

The POST parameter result (`result` and `opensslResult`) sent via the “payment page back URL” will contain the following keys: `externalOrderId`, `identifier`, `status`, `customerId`, `orderId`, `cardId`, `transactionId`, `timestamp`, `custom` and `customField`

 

Note! `result` is only used for backwards compatibility and we recommend using only `opensslResult` 

Note! The result parameter will be encrypted.



Here is an example of a `result` (after being decrypted):

```php
Array(
    [status] => complete-ok
    [externalOrderId] => external-order-id
    [identifier] => identifier
    [customerId] => 1
    [orderId] => 123
    [cardId] => 3
    [transactionId] => 1234
    [custom] => Array(
        [key_1] => value_1
        [key_2] => value_2
    )
    [customField] => Array()
    [timestamp] => 1485939750
)

```

The `status` key can have the following values: in-progress (only the AUTH was made), complete-ok (complete, successful transaction), complete-failed (failed transaction).



The response you receive from Twispay can be decrypted in the following way:

·   split the encrypted response by first comma (,);

·   on the left side, you will get the IV (initialization vector);

·   on the right side, you will get the encrypted data;

·   decode both IV and data by using base64 algorithm;

·   use openssl decrypt providing data, aes-256-cbc cipher, your secret key and the IV;

·   the resulted decrypted value is the data, in JSON format.

Here is an example of the decryption function (in PHP) for `opensslResult`:

```php
function decrypt($encrypted, $key = 'YOUR API KEY HERE') 
    {
        $encrypted = (string)$encrypted;
        if(!strlen($encrypted)) {
            return null;
        }
        if(strpos($encrypted, ',') !== false) {
            $encryptedParts = explode(',', $encrypted, 2);
            $iv = base64_decode($encryptedParts[0]);
            if (false === $iv) {
                throw new Exception("Invalid encryption iv");
            }
            $encrypted = base64_decode($encryptedParts[1]);
            if (false === $encrypted) {
                throw new Exception("Invalid encrypted data");
            }
            $decrypted = openssl_decrypt($encrypted, “aes-256-cbc”, $key, OPENSSL_RAW_DATA, $iv);
            if (false === $decrypted) {
                throw new Exception("Data could not be decrypted");
            }
            return $decrypted;	
        }
    return null;   
    }

```

Note! Make sure you add your own apiKey.

After the transaction is completed (either as successful or failed), the client can be redirected to a URL (payment page back URL) specified by the merchant. 

# Testing the integration

 

Once the integration is complete you can test different transaction results by using the following amounts: 

| **Amount**                                            | **Result**                                     |
| ----------------------------------------------------- | ---------------------------------------------- |
| 0.02                                                  | Insufficient  funds                            |
| 0.03                                                  | Bank time out                                  |
| 0.04                                                  | Pending  at Bank                               |
| 0.05                                                  | Declined by Bank                               |
| 0.06                                                  | Rejected  by Processor                         |
| 0.07                                                  | Malformed response from Processor              |
| 0.08                                                  | Timeout  (processor hangs in an infinite loop) |
| Over  25                                              | 3D Secure transaction                          |
| Any other value                                       | Approved  (complete-ok)                        |
| **For tests  you can use the following card details** |                                                |
| Card number                                           | 4111  1111 1111 1111                           |
| CVV/CVC                                               | 123                                            |
| Expiration date                                       | Any  date in the future                        |
| 3D Secure  code                                       | 00000                                          |

# Custom payment page specifications

 

You have the option to use a default template or create your own custom payment page and send it to us (HTML & CSS); the files will be verified and uploaded to your account. 

When building a custom payment page you can use the following tags (the required ones are marked accordingly)

| **Tag  name**                                                | **Description**                                              | **Mandatory** |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------- |
| {{ error }}                                                  | payment form error                                           | yes           |
| {{ cardId }}                                                 | card selector for choosing one  of the already used cards    | no            |
| {{ cardHolderName }}                                         | card holder name                                             | yes           |
| {{ cardHolderCountry }}                                      | card holder country                                          | no            |
| {{ cardHolderState }}                                        | card holder state                                            | no            |
| {{ cardType }} or              {{ cardTypeSelect }} or {{ cardTypeImage }} | card type as radio button list  / card type as a drop down list / card type as a drop down list of images  (need to be inserted just under the `cardNumber` tag) | yes           |
| {{ cardNumber }}                                             | card number                                                  | yes           |
| {{ cardExpiryDate }}                                         | card expiry date (month and year)                            | yes           |
| {{ cardCvv }}                                                | card cvv                                                     | yes           |
| {{ invoiceEmail }}                                           | invoice email                                                | no            |
| {{ firstName }}                                              | customer first name                                          | no            |
| {{ lastName }}                                               | customer last name                                           | no            |
| {{ country }}                                                | customer country                                             | no            |
| {{ state }}                                                  | customer state                                               | no            |
| {{ city }}                                                   | customer city                                                | no            |
| {{ zipCode }}                                                | customer zip code                                            | no            |
| {{ address }}                                                | customer address                                             | no            |
| {{ phone }}                                                  | customer phone                                               | no            |
| {{ email }}                                                  | customer email                                               | no            |
| {{ submit }}                                                 | payment form submit button                                   | yes           |
| {{ cart }} or                            {{ cartLiner }}     | the cart content  or payment description and the total amount / the cart content or payment  description and the total amount in a simpler presentation | yes           |
| {{ currency }}                                               | order currency                                               | no            |
| {{ description }}                                            | order description                                            | no            |
| {{ amount }}                                                 | order amount                                                 | no            |
| {{ logo }}                                                   | a logo image                                                 | no            |
| {{ language }}                                               | a way for the user to change  the language                   | no            |
| {{ closeModal }}                                             | close modal button                                           | no            |
| {{ termsAndConditions }}                                     | terms & conditions with a  link                              | yes           |
| {{ descriptor }}                                             | the site descriptor                                          | yes           |
| {{ merchant }}                                               | name of the merchant                                         | yes           |
| {{ mandatoryFields }}                                        | a description of mandatory fields                            | no            |

Note! You can set a default language for your payment page to load in by sending GET parameter ?lang=<language_code> in the URL. The following language options are available at the moment:           en, fr, de, ro, es, it, ru (example: https://secure.twispay.com/card?lang=it)

To provide a custom payment form field (only text fields):

 ```
 \{\{ customField({
  'name': 'unique-name',
  'label': 'Field label',
  'placeholder': 'Field placeholder',
  'error': 'This field is required',
  'required': true,
  'lang': 'en'
}) \}\}
```

Note! The `lang` key, by default will be set to `en`. If you want to provide translations, just include the same custom field definition, using the same `name`, in different language (the `lang` key will need to be set with the two chars language code).

 To provide custom text in multiple languages:

```
\{\{ translate({
  'en': 'Some text in english',
  'fr': 'Quelques textes en anglais'
}) \}\}
```

Note! The key is the language (provided in two chars language code) and the value is the text in that language. The `en` should always be defined. If current page's language is not defined - the `en` version will be used (or the first defined language when `en` is undefined). 

Note! You can also use this online tool to create a customized design for your Twispay payment page: 

https://secure-stage.twispay.com/build.php

When you are done, download the HTML and CSS files and send them for review to the Twispay technical team at the following email address: [*support@twispay.com*](mailto:support@twispay.com)

# iFrame payment form

 

If you want to implement a custom checkout experience for your customers and accept online payment on your website without having to be PCI DSS compliant, we recommend using the iFrame payment form. Instead of redirecting the customers to our hosted payment page, they will remain on your website to make the payment and twispay® will securely collect the necessary payment information and get it authorized for you; all the sensitive card data will be collected and managed by twispay®.

Note! Make sure to select the pop-up payment page option when selecting the template in your merchant account (Sites > Website details > Template).

The iFrame element integration has two options:

 

**Option 1: Using twispay® SDK to create the iFrame**

 

This is the easiest option to implement and it has two simple steps:

1. In JavaScript, declare a global variable named ‘twispayButtonsSelectors’; the value must be an array that contains the id or class of the button/s used used to submit to the payment form.

 



```html
Single button example: 

<script type="text/javascript">
	var twispayButtonsSelectors = [
		'#payButton'
	];
</script>

Multiple buttons example:

<script type="text/javascript">
	var twispayButtonsSelectors = [
		'.payButton',
		'#monthlyPayButton',
		'#twispayPayButton'
	];
</script>
```

2. Insert the SDK in the page after the global variable declaration

```html
<script type="text/javascript" src="https://secure.twispay.com/assets/script"></script>
<script type="text/javascript" src="https://secure-stage.twispay.com/assets/script"></script> (for the test environment)

```



**Option 2: Using twispay® SDK on your custom iFrame element**

 

The iFrame element/s must be present on the page and the ‘display’ property set to ‘none’. This implementation is comprised of three steps:

 

1. In JavaScript, declare a global variable named ‘twispayIFramesSelector’; the value must be an array that contains the id or class of the iFrame element/s used as target for the payment form.

```html
Single iFrame example:

<script type="text/javascript">
	var twispayIFramesSelectors = ['#paymentFrame'];
</script> 

Multiple iFrame elements example:

<script type="text/javascript">
	var twispayIFramesSelectors = [
        '#monthlySubscription',
        '#yearlySubscription',
        '#unlimitedSubscription'
    ];
</script> 

```



2. Add listeners on the submit button so that when the button is clicked, the right iFrame is shown by changing the ‘display’ property to ‘block’.

```html
ES5 on single iFrame example:

<script type="text/javascript">
	window.onload = function() {
		// obtain the submit button based on id attribute
		var payButton = document.getElementById("payButton");
		// add an event listener on the submit button so it will listen for click events
		payButton.addEventListener("click", function(event) {
			// when the button is pressed,
			// change the display attribute to block on the IFRAME element
			document.getElementById('monthlySubscription').style.display = "block";
		});
	};
</script> 

ES5 on multiple iFrame example:

<script type="text/javascript">
	window.onload = function() {
        var buttons = document.getElementsByClassName("payButton");
        for (var i = 0; i < buttons.length; i++) {
            var button = buttons.item(i);
            bindPayButton(button);
        }
    };

    function bindPayButton(button) {
        var parentForm = button.parentElement;
        var frameName = parentForm.getAttribute('target');
        var frameElement = document.getElementsByName(frameName)[0];
        button.addEventListener('click', function(event) {
            frameElement.style.display = "block";

        });
    }
</script> 

jQuery one iFrame element example:

<script type="text/javascript">
	$(document).ready(function() {

        $('#payButton').on('click', function(evt) {

            $('#fff').css({
                'display': 'block'
            });
        });
    });
</script> 

jQuery multiple iFrame elements example:

<script type="text/javascript">
	$(document).ready(function() {

        $('.submitButton').each(function(index, element) {
            bindButton(element);
        });

    });

    function bindButton(element) {
        var formElem = $(element).closest('form');
        var targetFrame = $(formElem).attr('target');
        var iframeElem = $("iframe[name='" + targetFrame + "']").get(0);

        $(element).on('click', function(evt) {

            $(iframeElem).css({
                'display': 'block'
            });

        });
    }
</script> 

```

3. Insert the SDK into the page after the global variable declaration

```html
<script type="text/javascript" src="https://secure.twispay.com/assets/custom"></script>
<script type="text/javascript" src="https://secure-stage.twispay.com/assets/custom"></script> (for the test environment)
```

There is a known issue with Safari on iOS where Safari by default discards cookies set in an iFrame unless the host that's serving the iframe has set a cookie before, outside the iframe.

This means that when customers try to make a payment on that site that integrated our payment page inside in iFrame they will get a "Session expired" error.

 

In order to solve this issue, the cookies need to be set in advance and the way to do this is to redirect the customer to an intermediary page (URL provided by Twispay) before the payment form is submitted. We will immediately redirect the customer back to the merchant website to a static URL (provided by the merchant to Twispay’s tech team).

 

After these two browser redirects, the customer can be sent inside the iFrame to make the payment. 

For more details about this issue and in order to obtain and provide the redirect URLs please contact a Twispay technical representative at the following email address: [*support@twispay.com*](mailto:support@twispaycom)

# Custom email receipt specifications

 

You can also customize the email receipt sent to the customers after a payment. You can send us the file once you build it and we will check and upload it to your account.

The following tags can be used (the required ones are marked accordingly):

 

| **Tag  name**     | **Description**                | **Mandatory** |
| ----------------- | ------------------------------ | ------------- |
| {{ logo }}        | a logo image                   | no            |
| {{ merchant }}    | merchant name                  | no            |
| {{ customer }}    | customer name                  | no            |
| {{ date }}        | the transaction date and time  | yes           |
| {{ transaction }} | the transaction ID             | yes           |
| {{ description }} | transaction/order  description | yes           |
| {{ amount }}      | the amount                     | yes           |
| {{ currency }}    | the amount currency            | yes           |

 

# Payout transactions for gambling (OCT/CFT)

 

For those merchants that require to do payouts to their customers the flow is as follows:

 

1   The initial deposit Order /POST is done with the required [request parameters](#_Request_parameters)

2   In the [response](#_Response_parameters) from us you will receive a ‘cardId’ parameter – this is the ID of the card used by the customer to do the initial deposit transaction

3   Using the ‘cardId’ you can initiate a payout transaction by making an API call to POST /order (documentation found at http://docs.twispay.com/#!/Order/post_order) using the following mandatory parameters:

 

 

| **Name**            | **Type / Length**                                            | **Mandatory** |
| ------------------- | ------------------------------------------------------------ | ------------- |
| customerId          | integer – the ID of the customer provided by twispay® after the initial deposit transaction | yes           |
| ip                  | string – customer’s IP address                               | yes           |
| amount              | float                                                        | yes           |
| currency            | string – use [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217)  alpha codes; must be the same as the currency of the deposit transaction | yes           |
| orderType           | string – set value to ‘credit’                               | yes           |
| transactionMethod   | string – set value to ‘card’                                 | yes           |
| cardTransactionMode | string – set value to ‘credit’                               | yes           |
| cardId              | string – the ID of the card used for the initial deposit  transaction (provided by twispay®) | yes           |
| description         | string – details about the product / service                 | no            |
| externalOrderId     | string – external order identifier (32 char limit)           | no            |

 

Note! The ‘description’ and ‘externalOrderId’ parameters are not mandatory but highly recommended to send because it helps merchants keep track of the orders and also helps identify what is the object of the transaction

Twispay API can be called at the following URLs:

For production, the API URL is: https://api.twispay.com

**For test, the API URL is:** **[https://api-stage.twispay.com](https://api-stage.twispay.com )**

 To authenticate use “Authorization:Bearer” together with your website apiKey in the header when making the request to our API; ex: “Authorization: Bearer 228c7055be1e78954675475674”


