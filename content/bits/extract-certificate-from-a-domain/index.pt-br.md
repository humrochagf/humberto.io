---
title: "Extrair certificado de um domínio"
publishDate: 2025-07-06
slug: extrair-certificado-de-um-dominio
tags:
  - openssl
  - certificate
  - pem
  - public-key
  - fingerprint

---

Para extrair o certificado use:

```shell
openssl s_client -showcerts -servername humberto.io -connect humberto.io:443 </dev/null 2>/dev/null
```

Se o seu alvo não usa [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication) você não precisa usar a opção `-servername <fqdn>`.

Em seguida você pode concatenar outros commandos OpenSSL para extrair informação no formato que quiser.

Para extrair o certificado no formato PEM:

```shell
| openssl x509 -outform PEM
```

Para extrair a chave publica:

```shell
| openssl x509 -pubkey -noout
```

Para extrair a impressão digital:

```shell
| openssl x509 -fingerprint -noout
```
