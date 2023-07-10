---
title: "Extraire le certificat et la clé d'un fichier .pfx"
publishDate: 2023-07-10
slug: extraire-le-certificat-et-la-cle-d-un-fichier-pfx
tags:
  - openssl
  - certificate
  - key
  - pfx
  - pem
  - rsa

---

Extrayez le certificat du fichier `.pfx`:

```shell
openssl pkcs12 -in <DOT_PFX_CERTIFICATE> -clcerts -nokeys -chain -out certificate.crt
```

Extrayez la clé privée au format `.pem` sans cryptage du fichier `.pfx`:

```shell
openssl pkcs12 -in <DOT_PFX_CERTIFICATE> -nocerts -nodes -out private_key.pem
```

Convertissez le `.pem` au format de clé `RSA`:

```shell
openssl rsa -in private_key.pem -out id_rsa
```

Si vous utilisez OpenSSL 3, le format de sortie par défaut sera "PKCS8", avec des limites d'encapsulation au format suivant:

```text
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
```

Si vous avez besoin de la clé au format `PKCS1`, c'est-à-dire traditionnel, utilisez la flag `-traditional` comme ceci:

```shell
openssl rsa -in private_key.pem -traditional -out id_rsa
```

La commande renverra la clé avec des limites d'encapsulation comme ceci:

```text
-----BEGIN RSA PRIVATE KEY-----
-----END RSA PRIVATE KEY-----
```
