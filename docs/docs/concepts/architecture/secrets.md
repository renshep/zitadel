---
title: Secrets
---

In this chapter you can find information of how ZITADEL processes and stores secrets and credentials in a secure fashion. 

:::info
We use the terms secret and credentials interchangeable to keep this guide lean.
:::

## Secrets Principles

ZITADEL uses the following principles when handling Secrets across their lifecycle:

- Automate rotation
- Limit lifetime
- Show only once
- Prefer public / private key concepts (FIDO2, U2F, JWT Profile, ...)
- Irreversible hash secrets / passwords
- Encrypt secrets storage
- When sent across unsecure channels (eMail, SMS, ...)
  - Forced changed through receiver
  - Verify that the secret can only be used once

## Secrets Storage

By default ZITADEL stores secrets from its users, clients as well as its generated secrets like signing keys in the database.
To protect the secrets against extraction from database as well as database dumps they are encrypted with AES256.

:::info
The key used to encrypt and decrypt the secrets in the ZITADEL database is called `masterkey` and needs to be exactly 32 bytes long.
The only secrets stored outside of the Secrets Storage are the masterkey, the TLS Keys, the initial Admin User (including the password)
:::

## Secrets stored in the Secrets Storage

### Public Keys

ZITADEL does handle many different public keys. These include:

- FIDO2
- U2F
- JWT Profile
- Signing Keys

:::info
Due to the inherent nature of a public key being public we safeguard them against malicious key changes with our unique [eventstore concept](../eventstore/overview).
:::

### Hashed Secrets

ZITADEL does handle many different passwords and secrets. These include:

- User Authentication
  - Password
- Client / Machine Authentication
  - Client Secrets

:::info
ZITADEL uses `bcrypt` by default to store all Passwords and Client Secrets in an non reversible way to further reduce the risk of a Secrets Storage breach.
:::

### Encrypted Secrets

Some secrets cannot be hashed because they need to be used in their raw form. These include:

- Federation
  - Client Secrets of Identity Providers (IdPs)
- Multi Factor Authentication
  - TOTP Seed Values
- Validation Secrets
  - Verifying contact information like eMail, Phonenumbers
  - Verifying proof of ownership over domain names (DNS)
  - Resting accounts of users (password, MFA reset, ...)
- Private Keys
  - Token Signing (JWT, ...)
  - Token Encryption (Opaque Bearer Tokens)
  - Useragent Cookies (Session Cookies) Encryption
  - CSRF Cookie Encryption
- Mail Provider
  - SMTP Passwords
- SMS Provider
  - Twilio API Keys

:::info
By default ZITADEL uses `RSA256` for signing purposes and `AES256` for encryption
:::

## Secrets stored outside the Secrets Storage

### Masterkey

Since the Masterkey is used as means of protecting the Secrets Storage it cannot be stored in the storage.
You find [here the many ways how ZITADEL can consume the Masterkey](../../guides/manage/self-hosted/configure).

### TLS Material

ZITADEL does support end to end TLS as such it can consume TLS Key Material.
Please check our [TLS Modes documentation](../../guides/manage/self-hosted/tls_modes) for more details.

### Admin User

The initial Admin User of ZITADEL can be configured through [ZITADELs config options](../../guides/manage/self-hosted/configure).

:::info
To prevent elevated breaches ZITADEL forces the Admin Users password to be changed during the first login.
:::
