# Security


## RSA Private/Public key
  - Use OpenSSL (https://www.openssl.org/)
  - To generate RSA key with length 2048: `openssl genrsa -out private.key 2048`

## PFX file

This is created based on the instruction here: https://www.sslmentor.com/help/openssl-certificate-export-to-pfx

### Required inputs: 
  - private key in .pem format
  - certificate in .pem format
    - To convert from .cer to pem format: `openssl x509 -inform der -in DN4_API.cer -out DN4_API.pem`

### Generate the pfx file
  - Generation command: `openssl pkcs12 -export -out DN4_API.pfx -inkey private.key -in DN4_API.pem`
    - Need a secure password with more than 4 characters
	
### To extract the private/public keys from pfx file

All transform from ".pfx" file need the original password from pfx creation
  - Extract private & public key
    ```
	openssl pkcs12 -in .\DN4_API.pfx -nocerts -nodes -out private_key.pem
	openssl rsa -in .\private_key.pem -pubout -out public_key.pem
	```
	- Private key in pem format has extra header with pfx attributes
  - Extract certificate: `openssl pkcs12 -in .\DN4_API.pfx -nokeys -out .\DN4_API.pem`