# Encryption Technologies Lab

## Overview

This lab explored several core encryption and cryptography concepts from Security+ study, including hashing, symmetric encryption, asymmetric encryption, digital signatures, and TLS certificate inspection.

The goal was to connect the textbook concepts to real Linux command-line tools and observe how encryption technologies behave in practice.

## Objectives

By the end of this lab, I practiced how to:

* Generate and compare cryptographic hashes
* Explain the difference between hashing and encryption
* Encrypt and decrypt a file using symmetric encryption
* Understand how GPG uses passphrases and key material
* Generate and use GPG public/private keypairs
* Create and verify digital signatures
* Inspect TLS certificate information with OpenSSL
* Troubleshoot DNS issues that prevent TLS inspection

## Lab Environment

| Component        | Details                                      |
| ---------------- | -------------------------------------------- |
| Workstation      | goblinwrangler                               |
| Operating System | Linux Mint                                   |
| Lab Directory    | `~/labs/encryption-weekend`                  |
| Tools Used       | `sha256sum`, `gpg`, `openssl`, `resolvectl`  |
| Domain Tested    | `drewsinfosec.com`                           |
| Study Source     | Professor Messer Security+ encryption topics |

---

# Lab 1: Hashing vs Encryption

## Purpose

The first part of the lab demonstrated that hashing is a one-way process used for integrity checking, not for hiding data in a reversible way.

## Commands Used

```bash
mkdir -p ~/labs/encryption-weekend
cd ~/labs/encryption-weekend

echo "The launch code is Swordfish" > message.txt
cat message.txt
```

I generated several hashes of the file:

```bash
md5sum message.txt
sha1sum message.txt
sha256sum message.txt
sha512sum message.txt
```

Then I created a second file with a small change:

```bash
echo "The launch code is swordfish" > message2.txt
sha256sum message.txt message2.txt
```

## Result

Changing only one letter caused the hash value to change completely. This demonstrated the avalanche effect, where a small change in input creates a very different hash output.

## Security Concept

Hashing is used to verify integrity. If two files have different hashes, then the contents are not identical.

Hashing is not encryption because it is not designed to be reversed back into the original plaintext.

## Screenshot Evidence

Add screenshot:

* `sha256sum message.txt message2.txt`

Suggested caption:

> A small change in the file created a completely different SHA-256 hash, showing how hashes can be used to detect changes.

---

# Lab 2: Symmetric Encryption with GPG

## Purpose

This part of the lab demonstrated symmetric encryption using GPG. Symmetric encryption uses the same secret to encrypt and decrypt data.

## Commands Used

I encrypted the original message file:

```bash
gpg -c message.txt
```

This created an encrypted file:

```bash
message.txt.gpg
```

I attempted to view the encrypted file:

```bash
cat message.txt.gpg
```

The output was unreadable ciphertext.

I then decrypted the file:

```bash
gpg -o decrypted-message.txt -d message.txt.gpg
cat decrypted-message.txt
```

## Result

The encrypted file could not be read normally. After entering the correct passphrase, GPG decrypted the file and restored the original message.

## Key Question: Where is the GPG key?

In this lab, no permanent public/private keypair was created. The encryption key was derived from the passphrase entered during encryption.

The encrypted file contains ciphertext and metadata needed by GPG, but the secret itself is the passphrase.

## Security Concept

Symmetric encryption is fast and effective, but it creates a key-sharing problem. If another person needs to decrypt the file, they need the same passphrase.

The encrypted file can be sent openly, but the passphrase should be shared through a separate secure channel.

## Screenshot Evidence

Add screenshots:

* `gpg -c message.txt`
* `ls -l`
* `cat message.txt.gpg`
* `gpg -o decrypted-message.txt -d message.txt.gpg`
* `cat decrypted-message.txt`

Suggested caption:

> GPG created an encrypted `.gpg` file using a passphrase. The file could only be decrypted after entering the correct passphrase.

---

# Lab 3: Asymmetric Encryption with GPG

## Purpose

This part of the lab introduced asymmetric encryption using a public/private keypair.

In asymmetric encryption, the public key can be shared, while the private key must stay protected.

## Commands Used

I attempted to encrypt a file to a recipient:

```bash
gpg -e -r drewtest@example.com message.txt
```

At first, GPG returned an error:

```text
gpg: error retrieving 'drewtest@example.com' via WKD: No data
gpg: drewtest@example.com: skipped: No data
gpg: message.txt: encryption failed: No data
```

## Result

The error occurred because GPG could not find a public key for `drewtest@example.com` in the local keyring. GPG also attempted to retrieve the key using WKD, or Web Key Directory, but no public key was available online for that test address.

The fix is to either generate a keypair or use the exact email address attached to an existing local GPG key.

Useful check command:

```bash
gpg --list-keys
```

If no matching key exists, generate one:

```bash
gpg --full-generate-key
```

Then encrypt using the matching recipient email:

```bash
gpg -o message-asymmetric.gpg -e -r drewtest@example.com message.txt
```

Decrypt with:

```bash
gpg -o message-asymmetric-decrypted.txt -d message-asymmetric.gpg
cat message-asymmetric-decrypted.txt
```

## Security Concept

Asymmetric encryption solves the key-sharing problem found in symmetric encryption.

A person can share their public key openly. Others can use that public key to encrypt data for them. Only the matching private key can decrypt it.

## Screenshot Evidence

Add screenshots:

* The original GPG recipient error
* `gpg --list-keys`
* Successful encryption after creating or selecting the correct key
* Successful decryption

Suggested caption:

> GPG could not encrypt to the recipient until a matching public key was available in the local keyring.

---

# Lab 4: Digital Signatures

## Purpose

This part of the lab demonstrated how digital signatures can prove that a file has not been changed and that it was signed by the holder of a private key.

## Commands Used

I created a test file:

```bash
echo "This file was signed by Drew in the encryption lab." > signed-note.txt
```

Then I created a detached signature:

```bash
gpg --detach-sign signed-note.txt
```

I verified the signature:

```bash
gpg --verify signed-note.txt.sig signed-note.txt
```

Then I tampered with the file:

```bash
echo "Mwahaha I changed it" >> signed-note.txt
gpg --verify signed-note.txt.sig signed-note.txt
```

## Result

The original file passed signature verification. After the file was changed, verification failed.

## Security Concept

Digital signatures provide:

* Integrity
* Authentication
* Non-repudiation

A valid signature shows that the file has not changed since it was signed and that it was signed using the matching private key.

## Screenshot Evidence

Add screenshots:

* Successful signature verification
* Failed verification after tampering

Suggested caption:

> After the file was modified, the original digital signature no longer verified successfully.

---

# Lab 5: TLS Certificate Recon with OpenSSL

## Purpose

This part of the lab used OpenSSL to inspect TLS certificate information from HTTPS websites.

The goal was to connect TLS, certificates, certificate authorities, DNS, and PKI to real command-line output.

## Initial Command

I first attempted to inspect the TLS certificate for my own domain:

```bash
openssl s_client -connect drewsinfosec.com:443 -servername drewsinfosec.com
```

This failed with an error similar to:

```text
BIO_lookup_ex:system lib
Name or service not known
connect:errno=22
```

I then checked DNS resolution:

```bash
resolvectl query drewsinfosec.com
```

The result showed:

```text
drewsinfosec.com: resolve call failed: 'drewsinfosec.com' does not have any RR of the requested type
```

## Result

The TLS inspection failed because the domain had been purchased but no website or DNS address record had been configured yet.

In other words, the domain existed as a registered name, but it did not point to a web server.

## Root Cause

The root cause was not OpenSSL, TLS, or the certificate itself.

The root cause was DNS.

Before a TLS connection can happen, the client must resolve the hostname to an IP address using an A record or AAAA record. 
Since `drewsinfosec.com` did not resolve to an IP address, OpenSSL could not connect to port 443 and could not inspect a certificate.

## Successful Test Against a Working Site

To continue the lab, I tested a known working HTTPS site:

```bash
echo | openssl s_client -connect google.com:443 -servername google.com 2>/dev/null | openssl x509 -noout -issuer -subject -dates
```

I also tested another known working site:

```bash
echo | openssl s_client -connect cloudflare.com:443 -servername cloudflare.com 2>/dev/null | openssl x509 -noout -issuer -subject -dates
```

These commands displayed certificate details such as:

```text
issuer=
subject=
notBefore=
notAfter=
```

## Security Concept

TLS certificates help secure HTTPS connections by proving the identity of a site and allowing encrypted communication.

Important certificate fields include:

| Field     | Meaning                                               |
| --------- | ----------------------------------------------------- |
| Issuer    | The certificate authority that issued the certificate |
| Subject   | The identity the certificate was issued to            |
| notBefore | The date the certificate becomes valid                |
| notAfter  | The expiration date of the certificate                |

## Troubleshooting Lesson

This lab showed that TLS depends on DNS. A client cannot inspect a certificate if it cannot first resolve the hostname and connect to the server.

The troubleshooting path was:

```text
OpenSSL error
→ Check command syntax
→ Check DNS resolution
→ Identify missing DNS address record
→ Confirm no website had been configured for the domain
→ Test OpenSSL against known working HTTPS sites
```

## Screenshot Evidence

Add screenshots:

1. Failed OpenSSL command against `drewsinfosec.com`
2. `resolvectl query drewsinfosec.com`
3. Successful OpenSSL certificate inspection against `google.com`
4. Successful OpenSSL certificate inspection against `cloudflare.com`

Suggested caption:

> TLS inspection failed for `drewsinfosec.com` because the domain had not yet been pointed to a web server. 
A successful test against other HTTPS sites confirmed that OpenSSL was working correctly.

---

# Key Terms

| Term                  | Definition                                               |
| --------------------- | -------------------------------------------------------- |
| Plaintext             | Original readable data                                   |
| Ciphertext            | Encrypted unreadable data                                |
| Hash                  | One-way output used to verify integrity                  |
| Symmetric Encryption  | Same secret encrypts and decrypts                        |
| Asymmetric Encryption | Uses a public/private keypair                            |
| Public Key            | Can be shared and used for encryption or verification    |
| Private Key           | Must be protected and used for decryption or signing     |
| Digital Signature     | Cryptographic proof of integrity and authenticity        |
| TLS                   | Protocol used to secure web traffic                      |
| PKI                   | Public Key Infrastructure; trust system for certificates |
| Certificate Authority | Trusted organization that issues certificates            |
| A Record              | DNS record that maps a name to an IPv4 address           |
| AAAA Record           | DNS record that maps a name to an IPv6 address           |

---

# Reflection

This lab helped connect several Security+ encryption concepts to hands-on tools.

Hashing showed how integrity can be verified without hiding the original data. Symmetric encryption showed how a passphrase can protect a file, but also introduced the problem of securely sharing the secret. 
Asymmetric encryption showed how public/private keys solve that problem. Digital signatures demonstrated how cryptography can prove that a file has not been changed.

The TLS certificate lab also turned into a useful DNS troubleshooting lesson. I attempted to inspect the certificate for `drewsinfosec.com`, but the command failed because the domain had been registered without being pointed to a web server. 
This showed that DNS resolution must happen before a TLS connection and certificate exchange can occur.

Overall, this lab reinforced that cryptography is not just theory. It shows up in file protection, identity verification, secure websites, SSH, certificates, and everyday network communication.

## Next Steps

Possible follow-up labs:

* Set up a basic website for `drewsinfosec.com`
* Add DNS records through Cloudflare
* Inspect the TLS certificate after the site is live
* Generate SSH keys for homelab authentication
* Compare password login vs key-based SSH login
* Export and import a GPG public key between two lab machines
* Encrypt a file from one machine to another using the recipient’s public key
