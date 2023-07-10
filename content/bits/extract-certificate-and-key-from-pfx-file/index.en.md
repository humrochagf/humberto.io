---
title: "Extract certificate and key from .pfx file"
publishDate: 2023-07-10
tags:
  - openssl
  - certificate
  - key
  - pfx
  - pem
  - rsa

---

Extract the certificate from the `.pfx` file:

```shell
openssl pkcs12 -in <DOT_PFX_CERTIFICATE> -clcerts -nokeys -chain -out certificate.crt
```

Extract the private key in `.pem` format without encryption from the `.pfx` file:

```shell
openssl pkcs12 -in <DOT_PFX_CERTIFICATE> -nocerts -nodes -out private_key.pem
```

Convert the `.pem` into `RSA` key format:

```shell
openssl rsa -in private_key.pem -out id_rsa
```

If you are using OpenSSL 3 it will default it's output to be in `PKCS8` format, that contains encapsulation boundaries like this:

```text
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
```

If you need your key to be in `PKCS1` AKA traditional format use the `-traditional` flag like this:

```shell
openssl rsa -in private_key.pem -traditional -out id_rsa
```

This will give you the key with the following encapsulation boundaries:

```text
-----BEGIN RSA PRIVATE KEY-----
-----END RSA PRIVATE KEY-----
```
