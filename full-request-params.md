# Request parameters

| **Name**                                       |                      **Type / Length**                       | **Mandatory** |
| :--------------------------------------------- | :----------------------------------------------------------: | ------------- |
| **Authentication parameters**                    |                                                              |               |
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

## Cart Items Data

Bellow is explained the structure of an item array present in the „items” array. 

**Note!** Please consider that the sum of the items amounts must match the amount of the transaction.

| **Name**                       | **Type**  | **Details**                                                  |
| ------------------------------ | --------- | ------------------------------------------------------------ |
| order.items[0].item            | string  | name of the product                                          |
| order.items[0].unitPrice       | string  | float (can have negative value  for discounts, vouchers, etc.) – including VAT |
| order.items[0].units           | integer | quantity                                                     |
| order.items[0].type            | string    | string – one of: “physical”, “digital”                     |
| order.items[0].code            | string    | code of the item => string - varchar 64                    |
| order.items[0].vatPercent      | float     | VAT percent                                                |
| order.items[0].itemDescription | string    | description => string - varchar 500                        |

**Note!** You can validate your JSON using any JSON validator and the Twispay validation schema, which you can find here: [https://secure.twispay.com/schema/v1/order.schema.json](https://secure.twispay.com/schema/v1/order.schema.json).

**Note!** For Level 3 Data, item information (Cart/products details parameters) becomes mandatory besides the Level 3 Data additional request parameters listed in the table.

**Note!** The ‘orderId’ must be unique for each request since it is stored in the customer’s cookies and re-attempting to make a payment with the same ID will immediately return the same result as the initial submit.

Ex: if the first attempt failed, re-submitting with the same ID will take the customer directly onto the page where the fail message is displayed.


## 3-D Secure 2.0

In order to manage fraud while maintaining a positive consumer experience 3-D Secure 2.0 uses contextual data sharing between merchant and issuer. The new 3-D Secure 2.0 standard makes it possible to have significantly higher amounts of data between the merchant and the bank, which adds an additional layer of security and streamlines the checkout process. The data can be used by the bank to assess the risk of a transaction as part of its anti-fraud detection system.

In some instances, such as a cardholder travelling or making an unusual purchase, an issuer may suspect a  transaction to be suspicious; if some of the data elements match but other don’t the issuer may or may not ask the cardholder for additional info to verify their identity. 

As a merchant you can collect and pass this data to the payment gateway (Twispay®) when submitting the payment request.  You must add a new parameter to your request called ***“threeDSecureData”.\*** Inside this parameter you have to add the following data as Base64 encoded JSON.

| **Name**                 | **Type**  | **Details**                                                  |
| ------------------------ | --------- | ------------------------------------------------------------ |
| browserAcceptHeader      | string  | Between 2 and 2048 char; Exact content of the HTTP  accept headers as sent to the 3DS Requestor from the Cardholder’s browser |
| browserIP                | string  | Format ipv4 or ipv6; IP address of the  browser as returned by the HTTP headers to the 3DS Requestor |
| browserJavaEnabled       | boolean | Represents  the ability of the cardholder browser to execute Java. Value is returned from  the navigator.javaEnabled property |
| browserJavaScriptEnabled | boolean   | Boolean that represents the ability of the cardholder browser to execute JavaScript |
| browserLanguage          | string    | Between 1 and 8 char; Value representing the browser language as defined in IETF BCP47. Returned from navigator.language property |
| browserColorDepth        | string    | Obtained from Cardholder browser using the screen.colorDepth property |
| browserScreenHeight      | string    | Value is returned from the screen.height property; pattern: "^[1-9][0-9]{0,5}$" |
| browserScreenWidth       | string    | Value is returned from the screen.width property;   pattern: "^[1-9][0-9]{0,5}$" |
| browserTZ                | string    | Time-zone offset in minutes between UTC and the Cardholder browser local time. Note that the offset is positive if the local time zone is behind UTC and negative if it is ahead; "pattern": "^[\\+\\-]?[0-9]{1,5}$"|
| browserUserAgent         | string    | Between 2 and 2048 char                                      |
| cardholderName           | string    | Between 2 and 45 char                                        |
| Email                    | string    | Max length: 254 char; format email                           |
| homePhone                | object    | “cc” (Country code); type: string; pattern: "^[0-9]{1,3}$"  “subscriber” (Phone number); type: string; max  length: 15 char |
| mobilePhone              | object    | “cc” (Country code); type: string; pattern: "^[0-9]{1,3}$"  “subscriber” (Phone number); type: string; max length: 15 char |
| workPhone                | object    | “cc” (Country code); type: string; pattern: "^[0-9]{1,3}$"  “subscriber” (Phone number); type: string; max  length: 15 char |
| billAddrCity             | string    | Between 1 and 50 char                                        |
| billAddrCountry          | string    | [ISO 3166-1](https://en.wikipedia.org/wiki/ISO_3166-1) numeric three-digit country code, other than exceptions listed in Table A.5; pattern: "^[0-9]{3}$" |
| billAddrLine1            | string    | Between 1 and 50 char                                        |
| billAddrLine2            | string    | Between 1 and 50 char                                        |
| billAddrLine3            | string    | Between 1 and 50 char                                        |
| billAddrPostCode         | string    | Max length: 16 char                                          |
| billAddrState            | string    | Max length: 3 char; Should be the  country subdivision code defined in ISO 3166-2 |
| shipAddrCity             | string    | Between 1 and 50 char                                        |
| shipAddrCountry          | string    | [ISO 3166-1](https://en.wikipedia.org/wiki/ISO_3166-1) numeric three-digit country code, other  than exceptions listed in Table A.5; pattern: "^[0-9]{3}$" |
| shipAddrLine1            | string    | Between 1 and 50 char                                        |
| shipAddrLine2            | string    | Between 1 and 50 char                                        |
| shipAddrLine3            | string    | Between 1 and 50 char                                        |
| shipAddrPostCode         | string    | Between 1 and 16 char                                        |
| shipAddrState            | string    | Between 1 and 3 char; Should be the country subdivision code defined in [ISO 3166-2](https://en.wikipedia.org/wiki/ISO_3166-2) |
| addrMatch                | string    | Enum: “Y” or “N”; Indicates whether the Cardholder Shipping Address and Cardholder Billing Address are the same |

By default, Twispay® will collect the following data from the card holders: “**browserAcceptHeader**”, “**browserIP**”, “**browserJavaScriptEnabled**”, “**browserUserAgent**”, “**browserLanguage**”; also if the card holder has “ **browserJavaScriptEnabled**” = true, then we will also get the following information: “**browserColorDepth**”, “**browserScreenHeight**”, “**browserScreenWidth**” and “**browserTZ**”.

Sending this data inside the ***threeDSecureData\*** parameter will override the information collected by us.

**Note!** You can validate your JSON using any validator and the Twispay validation schema, which you can find here: [https://secure-stage.twispay.com/schema/3ds/3ds.schema.json](https://secure-stage.twispay.com/schema/3ds/3ds.schema.json)
