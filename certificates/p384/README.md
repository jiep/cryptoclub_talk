# P-384 with SHA-384 certificate chain
## Root CA

```bash
openssl req -x509 -nodes -days 365 -newkey ec -pkeyopt ec_paramgen_curve:secp384r1 -sha384 -keyout p384/root-key-p384.pem -out p384/root-cert-p384.pem -subj "/O=CryptoClub/OU=Classic Security/CN=P-384 Root CA" -addext "basicConstraints = critical, CA:true"
```

## Int CA

```bash
openssl req -new -nodes -newkey ec -pkeyopt ec_paramgen_curve:secp384r1 -sha384 -subj "/O=CryptoClub/OU=Classic Security/CN=P-384 Int CA" -keyout p384/int-key-p384.pem -out p384/int-csr-p384.pem

openssl x509 -req -days 365 -in p384/int-csr-p384.pem -CA p384/root-cert-p384.pem -CAkey p384/root-key-p384.pem -sha384 -CAcreateserial -extfile <(echo -e "basicConstraints=critical, CA:true\nkeyUsage=critical,digitalSignature,keyCertSign,cRLSign") -out p384/int-cert-p384.pem
```

## Server certificate

```bash
openssl req -new -nodes -newkey ec -pkeyopt ec_paramgen_curve:secp384r1 -sha384 -subj "/O=CryptoClub/OU=Classic Security/CN=localhost" -keyout p384/server-key-p384.pem -out p384/server-csr-p384.pem

openssl x509 -req -days 365 -in p384/server-csr-p384.pem -CA p384/int-cert-p384.pem -CAkey p384/int-key-p384.pem -sha384 -CAcreateserial -extfile <(echo -e "subjectAltName=DNS:localhost,IP:127.0.0.1\nextendedKeyUsage=serverAuth") -out p384/server-cert-p384.pem
```

## Trust chain

```bash
cat p384/int-cert-p384.pem p384/root-cert-p384.pem > p384/trust-chain-p384.pem
```
