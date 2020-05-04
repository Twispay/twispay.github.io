## Testing the integration

Once the integration is complete you can test different transaction results by submitting orders with the following amounts: 

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
