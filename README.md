# OTTO Encryptor — Firefox Extension

A Firefox WebExtension that encrypts/decrypts **text** and **files** using the **OTTO** format (AES-256-GCM + HKDF; HKDF‑SIV nonces; chunked container). It is wire‑compatible with the Laravel/PHP SDK and other OTTO SDKs.

## Features
- **Text**: produce `HEADER_B64` and `CIPHER_B64 (ct||tag)`; decrypts back.
- **Files**: encrypt any file (photo/audio/video/docs) to `*.otto`; decrypt `*.otto` back.
- **Key**: Base64 32‑byte key (generate with one click).
- Uses WebCrypto for HKDF‑SHA256 and AES‑GCM; deterministic HKDF‑SIV style 12‑byte nonces.

## Install (Temporary Add-on)
1. Download and unzip this folder.
2. Go to `about:debugging#/runtime/this-firefox` → **Load Temporary Add-on…**.
3. Select the unzipped folder’s `manifest.json`.
4. Click the toolbar icon to open the popup.

## How it works (format)
- **Header**: `"OTTO1" | 0xA1 | 0x02 | flags | 0x00 | u16_be(16) | file_salt[16]`
- **Keys**: `encKey = HKDF(rawKey32, salt=file_salt, info="OTTO-ENC-KEY", 32)`; `nonceKey = HKDF(..., info="OTTO-NONCE-KEY", 32)`
- **Nonce** (per chunk): `HKDF(nonceKey, salt="", info="OTTO-CHUNK-NONCE" || counter_be64, 12)`
- **AEAD**: AES‑256‑GCM (tag 16 B), **AAD = header**
- **Streaming container**: `header || [u32_be ct_len || ct || tag16]*`

## Permissions
- `downloads` — used to save encrypted/decrypted blobs to disk.

MIT © 2025 Ivan Doe
