# Demos 

## Prerrequisites

* [OpenSSL](https://www.openssl.org) >= 3.5.0
* [LibOQS](https://github.com/open-quantum-safe/liboqs)
* [oqs-provider](https://github.com/open-quantum-safe/oqs-provider)


## Demo 1 (ECDH and AUTH -> P-384)

```bash
# Server
openssl s_server \
	-num_tickets 0 \
	-cert p384/server-cert-p384.pem \
	-cert_chain p384/int-cert-p384.pem \
	-key p384/server-key-p384.pem \
	-www \
	-accept 4433 -tls1_3 \
	-ciphersuites "TLS_AES_256_GCM_SHA384" \
	-groups "secp384r1"
```

```bash
# Client
openssl s_client \
	-connect localhost:4433 \
	-CAfile p384/root-cert-p384.pem \
	-brief
```

* View on Chrome: `https://localhost:4433`.


## Demo 2 (KEM -> X25519MLKEM768; AUTH -> P-384)

```bash
# Server
openssl s_server \
	-num_tickets 0 \
	-cert p384/server-cert-p384.pem \
	-cert_chain p384/int-cert-p384.pem \
	-key p384/server-key-p384.pem \
	-www \
	-accept 4433 -tls1_3 \
	-ciphersuites "TLS_AES_256_GCM_SHA384" \
	-groups "SECP384R1MLKEM1024:X25519MLKEM768"
```

```bash
# Client
openssl s_client \
	-connect localhost:4433 \
	-CAfile p384/root-cert-p384.pem \
	-brief
```

* View on Chrome: `https://localhost:4433`.


## Demo 3 (KEM -> X25519MLKEM768; AUTH -> ML-DSA-87)

```bash
# Server
openssl s_server \
	-num_tickets 0 \
	-cert mldsa87/server-cert-mldsa87.pem\
	-cert_chain mldsa87/int-cert-mldsa87.pem\
	-key mldsa87/server-key-mldsa87.pem\
	-www \
	-accept 4433 -tls1_3\
	-ciphersuites "TLS_AES_256_GCM_SHA384"\
	-groups "SECP384R1MLKEM1024:X25519MLKEM768"
```

```bash
# Client
openssl s_client\
	-connect localhost:4433 \
	-CAfile mldsa87/root-cert-mldsa87.pem \
	-brief
```

* View on Chrome: `https://localhost:4433`.


## Demo 4 (KEM -> X25519MLKEM768; AUTH -> P-384 + ML-DSA-65)

```bash
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl s_server \
	-num_tickets 0 \
 	-provider oqsprovider -provider default \
 	-cert p384_mldsa65/server-cert-p384_mldsa65.pem \
 	-cert_chain p384_mldsa65/int-cert-p384_mldsa65.pem \
 	-key p384_mldsa65/server-key-p384_mldsa65.pem \
	-www \
	-accept 4433 -tls1_3 \
	-ciphersuites "TLS_AES_256_GCM_SHA384" \
	-groups "SECP384R1MLKEM1024:X25519MLKEM768"
```
  
```bash
# Client
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl s_client \
	-connect localhost:4433 \
	-provider oqsprovider -provider default \
 	-CAfile p384_mldsa65/root-cert-p384_mldsa65.pem \
	-brief
```
  
## Demo 5 (KEM -> SecP384R1MLKEM1024; AUTH -> P-521 + ML-DSA-87)

```bash
# Server
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl s_server -num_tickets 0 \
  -provider oqsprovider -provider default \
  -cert p521_mldsa87/server-cert-p521_mldsa87.pem \
  -cert_chain p521_mldsa87/int-cert-p521_mldsa87.pem \
  -key p521_mldsa87/server-key-p521_mldsa87.pem \
  -www -accept 4433 -tls1_3 \
  -ciphersuites "TLS_AES_256_GCM_SHA384" \
  -groups "SECP384R1MLKEM1024"
```

```bash
# Client
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl s_client -connect localhost:4433 \
  -provider oqsprovider -provider default \
  -CAfile p521_mldsa87/root-cert-p521_mldsa87.pem \
  -groups "SECP384R1MLKEM1024" \
  -brief
```
