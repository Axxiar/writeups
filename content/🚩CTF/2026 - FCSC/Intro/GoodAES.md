---
title: Good AES
tags:
  - crypto
  - intro
  - CTF
  - 🇫🇷
---

- Author: ElyKar

## Description

Le standard AES (Advanced Encryption Standard) est utilisé depuis sa conception dans dizaines de milliards de produits. Cet algorithme de chiffrement symétrique se trouve dans les ordinateurs, téléphones, voitures, montres… Il est utilisé pour protéger des données de toutes sortes, dans des contextes variés.

Il est donc primordial de savoir l’utiliser, pour chiffrer ou déchiffrer des données. Voilà un flag chiffré avec AES-128 en mode CBC, déchiffrez-le.

## Fichier fourni

`good-aes.py`
```py
from Crypto.Cipher import AES
from secret import FLAG

key = bytes([i for i in range(60, 76)])
cipher = AES.new(key, AES.MODE_CBC, iv = b"\x00" * 16)
ciphertext = cipher.encrypt(FLAG)

print(ciphertext.hex())
# 1417c97254a837b486e829faab5628df
```

## Solution

### Quick overview

Toutes les infos pour obtenir le flag sont dans le fichier good-aes.py fourni: la clef, le ciphertext et l'algo de chiffrement (avec ses parametres)
On peut utiliser la meme librairie pour dechiffrer le ciphertext.

### {part1}

```py
from Crypto.Cipher import AES

key = bytes([i for i in range(60, 76)])
ciphertext = bytes.fromhex("1417c97254a837b486e829faab5628df") # given encrypted flag

cipher = AES.new(key, AES.MODE_CBC, iv=b"\x00"*16)
plaintext = cipher.decrypt(ciphertext)

print(plaintext)
```

## Flag

<details> 
<summary>Spoiler</summary>

```
FCSC{9f5b136ae2}
```
</details>
