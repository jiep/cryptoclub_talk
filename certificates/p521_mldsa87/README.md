# P521+ML-DSA-87 certificate chain
## Root CA

```bash
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl req -x509 -nodes -days 365 \
  -provider oqsprovider -provider default \
  -newkey p521_mldsa87 \
  -keyout p521_mldsa87/root-key-p521_mldsa87.pem \
  -out p521_mldsa87/root-cert-p521_mldsa87.pem \
  -subj "/O=CryptoClub/OU=PQC security (hybrid)/CN=p521_mldsa87 Root CA" \
  -addext "basicConstraints = critical, CA:true"
```

## Int CA

```bash
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl req -new -nodes \
  -provider oqsprovider -provider default \
  -newkey p521_mldsa87 \
  -subj "/O=CryptoClub/OU=PQC security (hybrid)/CN=p521_mldsa87 Int CA" \
  -keyout p521_mldsa87/int-key-p521_mldsa87.pem \
  -out p521_mldsa87/int-csr-p521_mldsa87.pem
  
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl x509 -req -days 365 \
  -provider oqsprovider -provider default \
  -in p521_mldsa87/int-csr-p521_mldsa87.pem \
  -CA p521_mldsa87/root-cert-p521_mldsa87.pem \
  -CAkey p521_mldsa87/root-key-p521_mldsa87.pem \
  -CAcreateserial \
  -extfile <(echo -e "basicConstraints=critical, CA:true\nkeyUsage=critical,digitalSignature,keyCertSign,cRLSign") \
  -out p521_mldsa87/int-cert-p521_mldsa87.pem
```

## Server CA

```bash
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl req -new -nodes \
  -provider oqsprovider -provider default \
  -newkey p521_mldsa87 \
  -subj "/O=CryptoClub/OU=PQC security (hybrid)/CN=localhost" \
  -keyout p521_mldsa87/server-key-p521_mldsa87.pem \
  -out p521_mldsa87/server-csr-p521_mldsa87.pem
  
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl x509 -req -days 365 \
  -provider oqsprovider -provider default \
  -in p521_mldsa87/server-csr-p521_mldsa87.pem \
  -CA p521_mldsa87/int-cert-p521_mldsa87.pem \
  -CAkey p521_mldsa87/int-key-p521_mldsa87.pem \
  -CAcreateserial \
  -extfile <(echo -e "subjectAltName = DNS:localhost, IP:127.0.0.1\nkeyUsage=critical,digitalSignature,keyEncipherment\nextendedKeyUsage=serverAuth") \
  -out p521_mldsa87/server-cert-p521_mldsa87.pem
```