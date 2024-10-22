# Prepare environment

Docker - if you need it
```bash
docker build -t certification_practice .
docker run -itd -v .:/certification_practice --name certification_practice certification_practice
```

Prepare configuration files
```bash
cp -r ./cnf_example ./cnf
```

## Quick Start 

```bash
openssl genrsa -out ./output/ca.key 4096
openssl req -new -key ./output/ca.key -out ./output/ca.csr -config ./cnf/ca.ini
openssl x509 -req -in ./output/ca.csr -signkey ./output/ca.key -out ./output/ca.crt -days 36500 -extensions v3_ca -extfile ./cnf/ca.ini
openssl genrsa -out ./output/server.key 4096
openssl req -new -key ./output/server.key -out ./output/server.csr -config ./cnf/server.ini
openssl x509 -req -in ./output/server.csr -CA ./output/ca.crt -CAkey ./output/ca.key -CAcreateserial -out ./output/server.crt -days 36500 -extensions v3_req -extfile ./cnf/server.ini
```

## Generate CA 

Generate CA private key
```bash
# password protected
openssl genrsa -aes256 -out ./output/ca.key 4096

# without password protected
openssl genrsa -out ./output/ca.key 4096
```

Generate CA CSR - The Certificate Signing Request for the CA.
```bash
openssl req -new -key ./output/ca.key -out ./output/ca.csr -config ./cnf/ca.ini
```

Generate CA Ccertificate - The self-signed CA certificate.
```bash
openssl x509 -req -in ./output/ca.csr -signkey ./output/ca.key -out ./output/ca.crt -days 36500 -extensions v3_ca -extfile ./cnf/ca.ini
```

## Generate Server

Generate server private key, password is additional - Your private key for the server (password protected).
```bash
# password protected
openssl genrsa -aes256 -out ./output/server.key 4096

# without password protected
openssl genrsa -out ./output/server.key 4096
```

Generate server CSR - The Certificate Signing Request for the server.
```bash
openssl req -new -key ./output/server.key -out ./output/server.csr -config ./cnf/server.ini
```

Generate server Ccertificate - The self-signed server certificate.
```bash
openssl x509 -req -in ./output/server.csr -CA ./output/ca.crt -CAkey ./output/ca.key -CAcreateserial -out ./output/server.crt -days 36500 -extensions v3_req -extfile ./cnf/server.ini
```
