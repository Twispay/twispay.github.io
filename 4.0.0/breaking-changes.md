# Breaking changes in 4.0.0

We standardised the way the requests are sent to your shop after a payment is processed via backUrl and IPN (server-to-server). So no more differenes between them.
For a complete documentation of the new standard please check the documentation [here](../index.md#response-parameters)

## "code" and "message" are removed
Use `transactionStatus` field to determine if a transaction is successful or declined.

## "status" is removed
This field was an old duplicate of `transactionStatus` which holds the same data.

## "transactionKind" is removed
This field was an old duplicate of `transactionMethod` which holds the same data.

## "externalCustomData" is removed
This filed's data is now split accross `customData` and `customFields` fields.

## "custom" is renamed
This field is renamed to `customData`.

## "customField" is renamed
This field is renamed to `customFields`

## "timestamp" is now integer only
This field can no longer be an object as it was in some cases.

## changed type for integer and float values
Fields like `orderId`, `transactionId`, `customerId`, `cardId` are now integer values; while `amount` is now float (this means that if the amount is round you will not get the 2 decimals anymore)
