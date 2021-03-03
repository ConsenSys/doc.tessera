---
description: Get started with Tessera and send a payload
---

# Getting started

## Prerequisites

1. Install necessary [dependencies](Dependencies.md)

1. Install [Tessera](DistributionJar.md)

## Starting Tessera and sending a payload

### 1. Generate keys

Generate a key pair in files called `myKey.pub` and `myKey.key`.

!!! bash
    ```
    tessera -keygen -filename myKey
    ```

When prompted to enter a password, press enter to generate an unlocked key.

!!! caution
    We are using unlocked keys for educational purposes only. Ensure private keys are secured appropriately
    in production environments.

### 2. Create a configuration file

Create a file called `tessera.conf` and add the following properties.

!!! example "Tessera configuration file"
    ```json
    {
       "useWhiteList": false,
       "jdbc": {
           "username": "sa",
           "password": "",
           "url": "jdbc:h2:./target/h2/tessera1",
           "autoCreateTables": true
       },
       "serverConfigs":[
           {
               "app":"ThirdParty",
               "enabled": true,
               "serverAddress": "http://localhost:9081",
               "communicationType" : "REST"
           },
           {
               "app":"Q2T",
               "enabled": true,
               "serverAddress": "http://localhost:22222",
               "communicationType" : "REST"
           },
           {
               "app":"P2P",
               "enabled": true,
               "serverAddress":"http://localhost:9001",
               "sslConfig": {
                   "tls": "OFF"
               },
               "communicationType" : "REST"
           }
       ],
       "peer": [
           {
               "url": "http://localhost:9001"
           },
           {
               "url": "http://localhost:9003"
           }
       ],
        "keys": {
            "passwords": [],
            "keyData": [
                {
                    "privateKeyPath": "myKey.key",
                    "publicKeyPath": "myKey.pub"
                }
            ]
       },
       "alwaysSendTo": []
    }
    ```

### 3. Start Tessera node

```bash
tessera -configfile tessera.conf
```

!!! tip
    If the command line help is displayed, enable debugging and try again.

    ```bash
    tessera --debug -configfile tessera.conf
    ```

### 4. Confirm Tessera is running

Use the `upcheck` method to confirm Tessera is up and running.

=== "Request"

    ```bash
    curl http://localhost:9081/upcheck
    ```

=== "Result"

    ```bash
    I'm up!
    ```

### 5. Send a payload

With one node running, send a payload to yourself where the `from` and `to` values are the
[generated public key](#1-generate-keys) (`myKey.pub`).

=== "Request"

    ```bash
    curl -X POST \
      http://localhost:22222/send \
      -H 'Content-Type: application/json' \
      -d '{
        "payload": "SGVsbG8sIFdvcmxkIQ==",
        "from": "<TesseraPublicKey>",
        "to": ["<TesseraPublicKey>"],
        "privacyFlag": 0,
        "affectedContractTransactions": [],
        "execHash": ""
      }'
    ```

=== "Example"

    ```bash
    curl -X POST \
      http://localhost:22222/send \
      -H 'Content-Type: application/json' \
      -d '{
        "payload": "SGVsbG8sIFdvcmxkIQ==",
        "from": "UPLeS7ZHZm02feA3qPTXcRBziB1APYCsjZmHGlV6EkQ=",
        "to": ["UPLeS7ZHZm02feA3qPTXcRBziB1APYCsjZmHGlV6EkQ="],
        "privacyFlag": 0,
        "affectedContractTransactions": [],
        "execHash": ""
      }'
    ```

=== "Result"

    ```bash
    {"key": "5jZPbwuxncr7qrL9esCTnz3PByLlD50kdZy5JkW/JMKcFeTl7I3NeLIsje4C410EYb93HabhddoAVGpwgYF1LQ=="}
    ```

### 6. Receive a payload

Use the key received when [sending the payload](#5-send-a-payload) and recipient key (`myKey.pub`)
to receive the payload.

=== "Request"

    ```bash
    curl -X GET \
      http://localhost:22222/receive \
      -H 'Content-Type: application/json' \
      -d '{
        "key": "<PayloadKeyFromSend>",
        "to": "<TesseraPublicKey>"
      }'
    ```

=== "Example"

    ```bash
    curl -X GET \
      http://localhost:22222/receive \
        -H 'Content-Type: application/json' \
        -d '{
          "key": "5jZPbwuxncr7qrL9esCTnz3PByLlD50kdZy5JkW/JMKcFeTl7I3NeLIsje4C410EYb93HabhddoAVGpwgYF1LQ==",
          "to": "UPLeS7ZHZm02feA3qPTXcRBziB1APYCsjZmHGlV6EkQ="
        }'
    ```

=== "Result"

    ```bash
    {
      "payload": "SGVsbG8sIFdvcmxkIQ==",
      "affectedContractTransactions": [],
      "execHash": "",
      "privacyFlag": 0
    }
    ```

Where `SGVsbG8sIFdvcmxkIQ==` is `Hello, World!` in Base64.
