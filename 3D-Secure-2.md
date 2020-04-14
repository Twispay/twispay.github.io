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



**Example of JSON containing 3D Secure 2.0 data**   
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
