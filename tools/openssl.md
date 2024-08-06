# OpenSSL Cheat-Sheet

## Certificate Management

| Command | Description |
| --- | --- |
| `openssl req -new -key <key> -out <csr>` | Generate a new certificate signing request |
| `openssl req -x509 -key <key> -in <csr> -out <cert>` | Generate a self-signed certificate |
| `openssl x509 -in <cert> -text -noout` | Display the details of a certificate |
| `openssl x509 -in <cert> -pubkey -noout` | Extract the public key from a certificate |
| `openssl x509 -in <cert> -fingerprint -noout` | Display the fingerprint of a certificate |

## Key Management

| Command | Description |
| --- | --- |
| `openssl genrsa -out <key> 2048` | Generate a new RSA private key |
| `openssl rsa -in <key> -pubout -out <pub_key>` | Extract the public key from a private key |
| `openssl rsa -in <key> -out <new_key>` | Convert a private key to a different format |
| `openssl rand -hex 16` | Generate a random hex string |

## Certificate Signing

| Command | Description |
| --- | --- |
| `openssl ca -in <csr> -out <cert>` | Sign a certificate request |
| `openssl ca -config <config> -in <csr> -out <cert>` | Sign a certificate request with a custom configuration |
| `openssl verify -CAfile <ca> <cert>` | Verify a certificate against a CA file |

## Certificate Conversion

| Command | Description |
| --- | --- |
| `openssl pkcs12 -export -in <cert> -inkey <key> -out <file>` | Convert a certificate and key to PKCS#12 format |
| `openssl pkcs12 -in <file> -out <cert> -nodes` | Extract a certificate and key from a PKCS#12 file |
| `openssl x509 -in <cert> -outform DER -out <file>` | Convert a certificate to DER format |
| `openssl x509 -in <cert> -outform PEM -out <file>` | Convert a certificate to PEM format |

## Encryption and Decryption

| Command | Description |
| --- | --- |
| `openssl enc -aes-256-cbc -salt -in <file> -out <encrypted_file>` | Encrypt a file with AES-256-CBC |
| `openssl enc -d -aes-256-cbc -in <file> -out <decrypted_file>` | Decrypt a file encrypted with AES-256-CBC |
| `openssl dgst -sha256 FILE` | Calculate the SHA-256 hash of a file |
| `openssl dgst -md5 FILE` | Calculate the MD5 hash of a file |

## Miscellaneous

| Command | Description |
| --- | --- |
| `openssl version` | Display the OpenSSL version |
| `openssl s_client -connect <host>:<port>` | Connect to a server using SSL/TLS |
| `openssl s_server -accept <port> -cert <cert> -key <key>` | Start an SSL/TLS server |
| `openssl speed` | Run benchmark tests on OpenSSL algorithms |
| `openssl ciphers -v` | List all available ciphers |
| `openssl rand -base64 32` | Generate a random base64 string |
| `openssl rand -base64 -out <file> 32` | Generate a random base64 string and save it to a file |
| `openssl rand -out <file> 32` | Generate a random binary string and save it to a file |
| `openssl rand -hex 32` | Generate a random hex string |


## Generate Self Signed Certificate
1. Generate RSA
Save passphrase
 ```shell
openssl genrsa -aes256 -out ca-key.pem 4096
```
2. Generate a public CA Cert
would generate an x509 certificate valid for 3650 days
```shell
 openssl req -new -x509 -sha256 -days 3650 -key ca-key.pem -out ca.pem  
```
3. Verify certificate information is correct
```shell
openssl x509 -in ca.pem -text
```
4. Issue certificates for our servers
Generate private and public keys for the server
```shell
openssl genrsa -out cert-key.pem 4096
```
5. Create a certificate signing request (CSR)
```shell
 openssl req -new -sha256 -subj "/CN=yourcn" -key cert-key.pem -out cert.csr  
```
```shell
 openssl req -new -sha256 -subj "/CN=servername like home-logging" -key cert-key.pem -out cert.csr  
```
6. Create extfile with all the alternative names
```shell
echo "subjectAltName=DNS:your-dns.record,IP:257.10.10.1" >> extfile.cnf
echo "subjectAltName=IP:127.0.0.1" >> extfile.cnf      
```
7. Create the certificate
```shell
openssl x509 -req -sha256 -days 3650 -in cert.csr -CA ca.pem -CAkey ca-key.pem -out cert.pem -extfile extfile.cnf -CAcreateserial
```
8. Merge cert.pem and ca.pem into fullchain.pem
```shell
cat cert.pem > fullchain.pem
cat ca.pem >> fullchain.pem
```