# Decrypting the response

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

**Note!** Make sure you add your own apiKey.

After the transaction is completed (either as successful or failed), the client can be redirected to a URL (payment page back URL) specified by the merchant. 
