# RNQC ncrypto compatibility branch

This branch is a reduced `ncrypto` source tree maintained specifically for React Native Quick Crypto (RNQC). It is not a drop-in replacement for the full upstream `nodejs/ncrypto` API or an independently published package.

## Provenance

- Upstream repository: `https://github.com/nodejs/ncrypto`
- Base revision: `027d1e44bbded3e40549d8edb0887cd783de8e5d` (`v1.1.4`)
- License: MIT; see `LICENSE`

## Retained surface

- `include/ncrypto.h`
- `include/ncrypto/aead.h`
- `include/ncrypto/version.h`
- `src/ncrypto.cpp`
- `src/aead.cpp`

RNQC retains EVP key parsing and export, X.509 and SPKAC helpers, ciphers, digests, KDF support including Argon2, AEAD helpers, and the other utilities used by its native consumers.

## Deliberate reductions

Upstream build systems, tests, tools, ENGINE support, and unused legacy DH, DSA, EC, HMAC, RSA, SSL, and X.509 wrapper surfaces are omitted. Traditional PKCS#1 and SEC1 export uses OpenSSL 3 provider encoders rather than deprecated low-level key APIs. Keep additions limited to APIs required by RNQC and verify retained translation units against OpenSSL deprecation warnings before updating this branch.
