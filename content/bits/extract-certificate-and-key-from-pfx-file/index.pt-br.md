---
title: "Extrair certificado e chave de um arquivo .pfx"
publishDate: 2023-07-10
slug: extrair-certificado-e-chave-de-um-arquivo-pfx
tags:
  - openssl
  - certificate
  - key
  - pfx
  - pem
  - rsa

---

Extraia o certificado do arquivo `.pfx`:

```shell
openssl pkcs12 -in <ARQUIVO_PONTO_PFX> -clcerts -nokeys -chain -out certificate.crt
```

Extraia a chave privada no formato `.pem` sem cifragem do arquivo `.pfx`:

```shell
openssl pkcs12 -in <ARQUIVO_PONTO_PFX> -nocerts -nodes -out private_key.pem
```

Converta o `.pem` no formato de chave `RSA`:

```shell
openssl rsa -in private_key.pem -out id_rsa
```

Se você esta usando OpenSSL 3 o formato de saida padrão será `PKCS8`, com as barreiras de encapsulamento neste formato:

```text
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
```

Se você precisa da chave no formato `PKCS1`, tambem conhecido como tradicional use a flag `-tradicional` desta forma:

```shell
openssl rsa -in private_key.pem -traditional -out id_rsa
```

O comando vai retornar a chave com as barreiras de encapsulamento desta forma:

```text
-----BEGIN RSA PRIVATE KEY-----
-----END RSA PRIVATE KEY-----
```
