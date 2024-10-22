# Prepare environment - if you need it

```bash
docker build -t certification_practice .
docker run -itd -v .:/certification_practice --name certification_practice certification_practice
```

## Generate ca 

Generate CA private key
```bash
# password protected
openssl genrsa -aes256 -out ./ca.key 4096

# without password protected
openssl genrsa -out ./ca.key 4096
```

Generate CA CSR - The Certificate Signing Request for the CA.
```bash
openssl req -new -key ./ca.key -out ./ca.csr -config ./cnf/ca.ini
```

Generate CA Ccertificate - The self-signed CA certificate.
```bash
openssl x509 -req -in ./ca.csr -signkey ./ca.key -out ./ca.crt -days 3650 -extensions v3_ca -extfile ./cnf/ca.ini
```

## Generate Server

Generate server private key, password is additional - Your private key for the server (password protected).
```bash
# password protected
openssl genrsa -aes256 -out ./server.key 4096

# without password protected
openssl genrsa -out ./server.key 4096
```

Generate server CSR - The Certificate Signing Request for the server.
```bash
openssl req -new -key ./server.key -out ./server.csr -config ./cnf/server.ini
```

Generate server Ccertificate - The self-signed server certificate.
```bash
openssl x509 -req -in ./server.csr -CA ./ca.crt -CAkey ./ca.key -CAcreateserial -out ./server.crt -days 3650 -extensions v3_req -extfile ./cnf/server.ini
```
