# CryptoClub - TLS Demos 

## Prerrequisites

* [OpenSSL](https://www.openssl.org) >= 3.5.0
* [LibOQS](https://github.com/open-quantum-safe/liboqs)
* [oqs-provider](https://github.com/open-quantum-safe/oqs-provider)
* [Google Chrome](https://www.google.com/intl/es/chrome/) >= 150 

## OQS-provider installation

1. Download, compile and install [`LibOQS`](https://github.com/open-quantum-safe/liboqs).

> [!WARNING]
> `liboqs` must be installed on `$HOME/opt/oqs/lib`. Set `-DCMAKE_INSTALL_PREFIX=$HOME/opt/oqs` during CMake config.

2. Download, compile and install [`oqs-provider`](https://github.com/open-quantum-safe/oqs-provider).

> [!WARNING]
> `oqs-provider` requires `oqsprovider.so` to be in directory `$HOME/opt/oqs/lib/ossl-modules/`. Run `cp <build_dir>/lib/oqsprovider.so $HOME/opt/oqs/lib/ossl-modules/` after successfull compilation.

> [!NOTE]
> Only demos 4 and 5 require `oqs-provider`.

## Demo 1 (ECDH and AUTH -> P-384)

```bash
# Server
openssl s_server \
	-num_tickets 0 \
	-cert certificates/p384/server-cert-p384.pem \
	-cert_chain certificates/p384/int-cert-p384.pem \
	-key certificates/p384/server-key-p384.pem \
	-www \
	-accept 4433 -tls1_3 \
	-ciphersuites "TLS_AES_256_GCM_SHA384" \
	-groups "secp384r1"
```

```bash
# Client
openssl s_client \
	-connect localhost:4433 \
	-CAfile certificates/p384/root-cert-p384.pem \
	-brief
```

> [!TIP]
> View on Chrome: `https://localhost:4433`.


## Demo 2 (KEM -> X25519MLKEM768; AUTH -> P-384)

```bash
# Server
openssl s_server \
	-num_tickets 0 \
	-cert certificates/p384/server-cert-p384.pem \
	-cert_chain certificates/p384/int-cert-p384.pem \
	-key certificates/p384/server-key-p384.pem \
	-www \
	-accept 4433 -tls1_3 \
	-ciphersuites "TLS_AES_256_GCM_SHA384" \
	-groups "SECP384R1MLKEM1024:X25519MLKEM768"
```

```bash
# Client
openssl s_client \
	-connect localhost:4433 \
	-CAfile certificates/p384/root-cert-p384.pem \
	-brief
```

> [!TIP]
> View on Chrome: `https://localhost:4433`.


## Demo 3 (KEM -> X25519MLKEM768; AUTH -> ML-DSA-87)

```bash
# Server
openssl s_server \
	-num_tickets 0 \
	-cert certificates/mldsa87/server-cert-mldsa87.pem\
	-cert_chain certificates/mldsa87/int-cert-mldsa87.pem\
	-key certificates/mldsa87/server-key-mldsa87.pem\
	-www \
	-accept 4433 -tls1_3\
	-ciphersuites "TLS_AES_256_GCM_SHA384"\
	-groups "SECP384R1MLKEM1024:X25519MLKEM768"
```

```bash
# Client
openssl s_client\
	-connect localhost:4433 \
	-CAfile certificates/mldsa87/root-cert-mldsa87.pem \
	-brief
```

> [!TIP]
> View on Chrome: `https://localhost:4433`.


## Demo 3 (KEM -> X25519MLKEM768; AUTH -> ML-DSA-87; browser)

```bash
# Server
openssl s_server \
	-num_tickets 0 \
	-cert certificates/mldsa87/server-cert-mldsa87.pem\
	-cert_chain certificates/mldsa87/int-cert-mldsa87.pem\
	-key certificates/mldsa87/server-key-mldsa87.pem\
	-WWW \
	-accept 4433 -tls1_3\
	-ciphersuites "TLS_AES_256_GCM_SHA384"\
	-groups "SECP384R1MLKEM1024:X25519MLKEM768"
```

> [!TIP]
> View on Chrome: `https://localhost:4433/web/index.html`.


## Demo 4 (KEM -> X25519MLKEM768; AUTH -> P-384 + ML-DSA-65)

```bash
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl s_server \
	-num_tickets 0 \
 	-provider oqsprovider -provider default \
 	-cert certificates/p384_mldsa65/server-cert-p384_mldsa65.pem \
 	-cert_chain certificates/p384_mldsa65/int-cert-p384_mldsa65.pem \
 	-key certificates/p384_mldsa65/server-key-p384_mldsa65.pem \
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
 	-CAfile certificates/p384_mldsa65/root-cert-p384_mldsa65.pem \
	-brief
```
  
## Demo 5 (KEM -> SecP384R1MLKEM1024; AUTH -> P-521 + ML-DSA-87)

```bash
# Server
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl s_server -num_tickets 0 \
  -provider oqsprovider -provider default \
  -cert certificates/p521_mldsa87/server-cert-p521_mldsa87.pem \
  -cert_chain certificates/p521_mldsa87/int-cert-p521_mldsa87.pem \
  -key certificates/p521_mldsa87/server-key-p521_mldsa87.pem \
  -www -accept 4433 -tls1_3 \
  -ciphersuites "TLS_AES_256_GCM_SHA384" \
  -groups "SECP384R1MLKEM1024"
```

```bash
# Client
env LD_LIBRARY_PATH="$HOME/opt/oqs/lib" OPENSSL_MODULES="$HOME/opt/oqs/lib/ossl-modules" \
openssl s_client -connect localhost:4433 \
  -provider oqsprovider -provider default \
  -CAfile certificates/p521_mldsa87/root-cert-p521_mldsa87.pem \
  -groups "SECP384R1MLKEM1024" \
  -brief
```

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for the full license text.
