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
cat ./output/server.crt ./output/ca.crt > ./output/server_fullchain.crt
```

## Generate Root CA 

Generate Root CA private key
```bash
# password protected
openssl genrsa -aes256 -out ./output/root_ca.key 4096

# without password protected
openssl genrsa -out ./output/root_ca.key 4096
```

Generate Root CA CSR - The Certificate Signing Request for the Root CA.
```bash
openssl req -new -key ./output/root_ca.key -out ./output/root_ca.csr -config ./cnf/root_ca.ini
```

Generate Root CA Ccertificate - The self-signed Root CA certificate.
```bash
openssl x509 -req -in ./output/root_ca.csr -signkey ./output/root_ca.key -out ./output/root_ca.crt -days 36500 -extensions v3_ca -extfile ./cnf/root_ca.ini
```

## Generate Intermediate CA
Generate Intermediate CA private key
```bash
# password protected
openssl genrsa -aes256 -out ./output/intermediate_ca.key 4096

# without password protected
openssl genrsa -out ./output/intermediate_ca.key 4096
```

Generate Intermediate CA CSR - The Certificate Signing Request for the Intermediate CA.
```bash
openssl req -new -key ./output/intermediate_ca.key -out ./output/intermediate_ca.csr -config ./cnf/intermediate_ca.ini
```

Generate Intermediate CA Ccertificate - The self-signed Intermediate CA certificate.
```bash
openssl x509 -req -in ./output/intermediate_ca.csr -CA ./output/root_ca.crt -CAkey ./output/root_ca.key -CAcreateserial -out ./output/intermediate_ca.crt -days 36500 -extensions v3_intermediate_ca -extfile ./cnf/intermediate_ca.ini
```

Concatenate Intermediate CA | CA Certificates
```bash
cat ./output/intermediate_ca.crt ./output/root_ca.crt > ./output/intermediate_ca_fullchain.crt
```

## Generate Leaf

Generate leaf private key, password is additional - Your private key for the server (password protected).
```bash
# password protected
openssl genrsa -aes256 -out ./output/leaf.key 4096

# without password protected
openssl genrsa -out ./output/leaf.key 4096
```

Generate leaf CSR - The Certificate Signing Request for the leaf.
```bash
openssl req -new -key ./output/leaf.key -out ./output/leaf.csr -config ./cnf/leaf.ini
```

Generate leaf Ccertificate - The self-signed leaf certificate.
```bash
# from root_ca
openssl x509 -req -in ./output/leaf.csr -CA ./output/root_ca.crt -CAkey ./output/root_ca.key -CAcreateserial -out ./output/leaf.crt -days 36500 -extensions v3_req -extfile ./cnf/leaf.ini

# from intermediate ca
openssl x509 -req -in ./output/leaf.csr -CA ./output/intermediate_ca.crt -CAkey ./output/intermediate_ca.key -CAcreateserial -out ./output/leaf.crt -days 36500 -extensions v3_req -extfile ./cnf/leaf.ini
```

Concatenate leaf | CA Certificates
```bash
# from root_ca
cat ./output/leaf.crt ./output/root_ca.crt > ./output/leaf_fullchain.crt

# from intermediate ca
cat ./output/leaf.crt ./output/intermediate_ca_fullchain.crt > ./output/leaf_fullchain.crt
```
