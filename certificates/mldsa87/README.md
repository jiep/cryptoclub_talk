# ML-DSA-87 certificate chain
## Root CA

```bash
openssl req -x509 -nodes -days 365 -newkey ec -newkey mldsa87 -keyout mldsa87/root-key-mldsa87.pem -out mldsa87/root-cert-mldsa87.pem -subj "/O=CryptoClub/OU=PQC security (pure)/CN=ML-DSA-87 Root CA" -addext "basicConstraints = critical, CA:true"

openssl req -x509 -nodes -days 365 -newkey mldsa87 -keyout server-key-mldsa87.pem -out server-cert-mldsa87.pem -subj "/CN=localhost" -addext "subjectAltName = DNS:localhost, IP:127.0.0.1"
```

## Int CA

```bash
openssl req -new -nodes -newkey mldsa87 -subj "/O=CryptoClub/OU=PQC security (pure)/CN=ML-DSA-87 Int CA" -keyout mldsa87/int-key-mldsa87.pem -out mldsa87/int-csr-mldsa87.pem

openssl x509 -req -days 365 -in mldsa87/int-csr-mldsa87.pem -CA mldsa87/root-cert-mldsa87.pem -CAkey mldsa87/root-key-mldsa87.pem -CAcreateserial -extfile <(echo -e "basicConstraints=critical, CA:true\nkeyUsage=critical,digitalSignature,keyCertSign,cRLSign") -out mldsa87/int-cert-mldsa87.pem
```

## Server certificate

```bash
openssl req -new -nodes -newkey mldsa87 -subj "/O=CryptoClub/OU=PQC security (pure)/CN=localhost" -keyout mldsa87/server-key-mldsa87.pem -out mldsa87/server-csr-mldsa87.pem

openssl x509 -req -days 365 -in mldsa87/server-csr-mldsa87.pem -CA mldsa87/int-cert-mldsa87.pem -CAkey mldsa87/int-key-mldsa87.pem -CAcreateserial -extfile <(echo -e "subjectAltName=DNS:localhost,IP:127.0.0.1\nextendedKeyUsage=serverAuth") -out mldsa87/server-cert-mldsa87.pem
```

## Trust chain

```bash
cat mldsa87/int-cert-mldsa87.pem mldsa87/root-cert-mldsa87.pem > mldsa87/trust-chain-mldsa87.pem
```
