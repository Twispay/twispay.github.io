# Request parameters

| **Name**                                       |                      **Type / Length**                       | **Mandatory** |
| :--------------------------------------------- | :----------------------------------------------------------: | ------------- |
| *Authentication parameters*                    |                                                              |               |
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
