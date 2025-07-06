---
title: "Extraire le certificat d'un domaine"
publishDate: 2025-07-06
slug: extraire-le-certificat-d-un-domaine
tags:
  - openssl
  - certificate
  - pem
  - public-key
  - fingerprint

---

Pour extraire le certificat utilisez:

```shell
openssl s_client -showcerts -servername humberto.io -connect humberto.io:443 </dev/null 2>/dev/null
```

Si votre cible n'utilise pas [SNI](https://fr.wikipedia.org/wiki/Server_Name_Indication) vous pouvez enlever l'option `-servername <fqdn>`.

Après vous pouvez l'enchainer avec d'autres commandes OpenSSL pour extraire de l'information dans le format dont vous avez besoin.

Pour extraire le certificat dans le format PEM:

```shell
| openssl x509 -outform PEM
```

Pour extraire la clé publique:

```shell
| openssl x509 -pubkey -noout
```

Pour extraire son empreinte:

```shell
| openssl x509 -fingerprint -noout
```
