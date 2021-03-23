---
description: How to generate keys and store them in AWS Secrets Manager
---

# Store keys in AWS Secrets Manager

**Prerequisites:**

* [AWS Secrets Manager configured and running](../Configure/KeyVault/AWS-Secrets-Manager.md).

You can use Tessera to generate a private and public key pair in AWS Secrets Manager. The following
example generates a private and public key pair and saves them to AWS Secrets Manager with IDs
`Pub` and `Key`, and endpoint `<url>`:

```bash
tessera -keygen -keygenvaulttype AWS -keygenvaulturl <url>
```

The `-filename` option can be used to specify alternate IDs. Multiple key pairs can be generated at
the same time by providing a comma-separated list of values:

```bash
tessera -keygen -keygenvaulttype AWS -keygenvaulturl <url> -filename id1,id2
```