---
title: "Extract certificate from a domain"
publishDate: 2025-07-06
tags:
  - openssl
  - certificate
  - pem
  - public-key
  - fingerprint

---

To extract the certificate use:

```shell
openssl s_client -showcerts -servername humberto.io -connect humberto.io:443 </dev/null 2>/dev/null
```

If your target is not using [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication) you don't need to use the `-servername <fqdn>` option.

Then you can pipe it with other OpenSSL commands to extract information in the format that you need.

To extract PEM formatted cert:

```shell
| openssl x509 -outform PEM
```

To extract the public key:

```shell
| openssl x509 -pubkey -noout
```

To extract the Fingerprint:

```shell
| openssl x509 -fingerprint -noout
```
