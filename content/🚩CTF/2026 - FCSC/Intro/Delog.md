---
title: Delog 
tags:
  - crypto
  - intro
  - CTF
  - 🇫🇷
---

- Author: ElyKar

## Description

Le problème du logarithme discret est à la base de plusieurs primitives cryptographiques très utilisées aujourd’hui. J’ai chiffré mon flag avec une courbe elliptique, arriverez-vous à résoudre le log discret ?

## Script fourni

`delog.py`
```py
from fastecdsa.curve import Curve
from fastecdsa.point import Point

# from secret import FLAG
FLAG = "AB" # test, not from original file

# FRP256v1
p  = 0xF1FD178C0B3AD58F10126DE8CE42435B3961ADBCABC8CA6DE8FCF353D86E9C03
a  = 0xF1FD178C0B3AD58F10126DE8CE42435B3961ADBCABC8CA6DE8FCF353D86E9C00
b  = 0xee353fca5428a9300d4aba754a44c00fdfec0c9ae4b1a1803075ed967b7bb73f
n  = 0xf1fd178c0b3ad58f10126de8ce42435b53dc67e140d2bf941ffdd459c6d655e1
Gx = 0xB6B3D4C356C139EB31183D4749D423958C27D2DCAF98B70164C97A2DD98F5CFF
Gy = 0x6142e0f7c8b204911f9271f0f3ecef8c2701c307e8e4c9e183115a1554062cfb
FRP256v1 = Curve("FRP256v1", p, a, b, n, Gx, Gy)
G = FRP256v1.G

for f in FLAG:
    pt = ord(f) * G
    print(f"X: {hex(pt.x)}")
    print(f"Y: {hex(pt.y)}")
# X: 0xda3355ba0fa27146a228717649a6dcef2615a8de4f65dd49b2672766fe821e74
# Y: 0x5456886882f67039bfd4d541e49814f4bd2fa039e28598dadb504b9295a2063f
# X: 0x30d614541e97d4b4066fa8c6fc8f44ddc1a1fddded722a65d9037a80833d02aa
# Y: 0x4ba88007ac3d46944abbda9ab8dbb784ff848f100e1c5da9ee73c6a0b7ca41be
# X: 0x3ea72aee9626d5c9200a692d92d5f78165180f2946290aafd5e1b050a89ab583
# Y: 0xd8d5d788c2508f8fc399872a6a73b4d5f10a1bb8af8b3de97fb287c28c7f6575
# X: 0x30d614541e97d4b4066fa8c6fc8f44ddc1a1fddded722a65d9037a80833d02aa
# Y: 0x4ba88007ac3d46944abbda9ab8dbb784ff848f100e1c5da9ee73c6a0b7ca41be
# X: 0x7f8a27e35dc664e25c210fa4f34987e65aa4a17ba2a054be509edc292bc79a5e
# Y: 0xabb3af8edf1f7f2926a9d584b2b0736c69155ad323e929810ee3028b7dfe53a4
# X: 0x368f4d4e3899059d8cd0af1964efcf7b024ce75a62deaa3ff608713de3b7f832
# Y: 0x305a4a11d24e0dec6a9f8a3ae199054805654c92eef685025e4fd1dc0d62b256
# X: 0xe199ce4278c00f04f3dec53f72e13cf4e1bc6b10200b84e7a183f1a4f713705f
# Y: 0x29e1ca2ff8749e18bd715370bd657c4d29dbbdfb43986061cb221525c16def8e
# X: 0xd65012ba119a06b213663d0e03ffa0fc78617ff31fecbe66d1125f838edc374b
# Y: 0x1ffaa3957681f395c8d646d056036fe3f8709dfc5dbc60ea153b5633df231451
# X: 0xa1782e95510927b213e042a728c2cb25cb8f706b00fdd9d9b3ea1e2d49be70f1
# Y: 0xb48cce14739efbe46710ccade4ff76e6ab07171fc18c4e02d98f229c598c39d8
# X: 0xe16e742513e824fa49c09560544037c1b255012c993cfae59b8ccb14a9907585
# Y: 0xdf230ce411b7777a6128dc494c39a16444a84f17aa6d3092016dca6465ba2ca
# X: 0x3ea72aee9626d5c9200a692d92d5f78165180f2946290aafd5e1b050a89ab583
# Y: 0xd8d5d788c2508f8fc399872a6a73b4d5f10a1bb8af8b3de97fb287c28c7f6575
# X: 0xd65012ba119a06b213663d0e03ffa0fc78617ff31fecbe66d1125f838edc374b
# Y: 0x1ffaa3957681f395c8d646d056036fe3f8709dfc5dbc60ea153b5633df231451
# X: 0xe199ce4278c00f04f3dec53f72e13cf4e1bc6b10200b84e7a183f1a4f713705f
# Y: 0x29e1ca2ff8749e18bd715370bd657c4d29dbbdfb43986061cb221525c16def8e
# X: 0x6b57d3dce59f1b03c7158f93564119900f952b611d0c1ed676f05adb41289ccc
# Y: 0xb236ddf8440286f7c83592501624a1fb4a397d03225aa30ba2d05ba28807e465
# X: 0x1a4eb41ec5147dc9fce761bf87637123c27814ecd9975080b9de2b92b9e54e82
# Y: 0xdadb7940d9342d0738b719445960ab50999f303e82dd43c77d5de6b64278464
# X: 0x114913a67b61c4ee5177deaeecf1138a7ddfbe9dc36a1028d85dc95ee9b94a68
# Y: 0x6f37abaac11439fd6394370eb746c26bee24024d06e142a3fb81bc28af63ce9a
# X: 0x9a6a44095380b935ad41761197f91e99ecb9a9777557de2d51e34dda32e47844
# Y: 0x9cd0e8099a4341f9521bb0fd610edefc4bbe185ac864e2d8d6b1b8b58eea7b3
# X: 0xbba2fac88d029643d8557b9850255c516d1851bad36184718ae3e416a5362b3
# Y: 0xdddac00e5bf9d3ec2d5d472756cc3ee38b229118eafb9aacc2466d7673fe4ed4
# X: 0x32c52d265ab09d061ec9f035ef61a1af5a83ea92104ae9d2ddf9f99904155d53
# Y: 0xb247a17ba9e07c128af69c824553cb0ea3bf4bbe994955bce2923cc7ee098d17
```

## Solution

### Quick overview

On a une courbe elliptique avec tous les paramètres : son base point (Gx,Gy ou G), ses facteurs (a,b), le modulo (p), l'ordre du base point (n).
On a tous les points obtenus par multiplication des caractères du FLAG avec le base point.
Il nous suffirait donc de précalculer pour tous les caractères possibles quel est sa valeur sur la courbe, puis reconvertir chaque points donné dans l’énoncé en son caractère correspondant.

### Script de solution

```py
from fastecdsa.curve import Curve

# On recréer la courbe elliptique avec les mêmes paramètres
p  = 0xF1FD178C0B3AD58F10126DE8CE42435B3961ADBCABC8CA6DE8FCF353D86E9C03
a  = 0xF1FD178C0B3AD58F10126DE8CE42435B3961ADBCABC8CA6DE8FCF353D86E9C00
b  = 0xee353fca5428a9300d4aba754a44c00fdfec0c9ae4b1a1803075ed967b7bb73f
n  = 0xf1fd178c0b3ad58f10126de8ce42435b53dc67e140d2bf941ffdd459c6d655e1
Gx = 0xB6B3D4C356C139EB31183D4749D423958C27D2DCAF98B70164C97A2DD98F5CFF
Gy = 0x6142e0f7c8b204911f9271f0f3ecef8c2701c307e8e4c9e183115a1554062cfb

curve = Curve("FRP256v1", p, a, b, n, Gx, Gy)
G = curve.G # G est base point de la Courbe

# Pour chaque caractère ascii qui contient lettres chiffres etc...
# c'est a dire de l'ascii 32 a 126 (ca devrait suffir pour un flag, a priori pas d'accent)
# On va venir précalculer ce que donne le point central de la courbe multiplie par notre caractère
# Le flag d'origine a subit la même transformation
lookup = {} 
for i in range(32, 127):
    pt = i * G
    lookup[(pt.x, pt.y)] = chr(i)

# Points donnés dans le sujet
points = [ 
(0xda3355ba0fa27146a228717649a6dcef2615a8de4f65dd49b2672766fe821e74,
 0x5456886882f67039bfd4d541e49814f4bd2fa039e28598dadb504b9295a2063f),

(0x30d614541e97d4b4066fa8c6fc8f44ddc1a1fddded722a65d9037a80833d02aa,
 0x4ba88007ac3d46944abbda9ab8dbb784ff848f100e1c5da9ee73c6a0b7ca41be),

(0x3ea72aee9626d5c9200a692d92d5f78165180f2946290aafd5e1b050a89ab583,
 0xd8d5d788c2508f8fc399872a6a73b4d5f10a1bb8af8b3de97fb287c28c7f6575),

(0x30d614541e97d4b4066fa8c6fc8f44ddc1a1fddded722a65d9037a80833d02aa,
 0x4ba88007ac3d46944abbda9ab8dbb784ff848f100e1c5da9ee73c6a0b7ca41be),

(0x7f8a27e35dc664e25c210fa4f34987e65aa4a17ba2a054be509edc292bc79a5e,
 0xabb3af8edf1f7f2926a9d584b2b0736c69155ad323e929810ee3028b7dfe53a4),

(0x368f4d4e3899059d8cd0af1964efcf7b024ce75a62deaa3ff608713de3b7f832,
 0x305a4a11d24e0dec6a9f8a3ae199054805654c92eef685025e4fd1dc0d62b256),

(0xe199ce4278c00f04f3dec53f72e13cf4e1bc6b10200b84e7a183f1a4f713705f,
 0x29e1ca2ff8749e18bd715370bd657c4d29dbbdfb43986061cb221525c16def8e),

(0xd65012ba119a06b213663d0e03ffa0fc78617ff31fecbe66d1125f838edc374b,
 0x1ffaa3957681f395c8d646d056036fe3f8709dfc5dbc60ea153b5633df231451),

(0xa1782e95510927b213e042a728c2cb25cb8f706b00fdd9d9b3ea1e2d49be70f1,
 0xb48cce14739efbe46710ccade4ff76e6ab07171fc18c4e02d98f229c598c39d8),

(0xe16e742513e824fa49c09560544037c1b255012c993cfae59b8ccb14a9907585,
 0x0df230ce411b7777a6128dc494c39a16444a84f17aa6d3092016dca6465ba2ca),

(0x3ea72aee9626d5c9200a692d92d5f78165180f2946290aafd5e1b050a89ab583,
 0xd8d5d788c2508f8fc399872a6a73b4d5f10a1bb8af8b3de97fb287c28c7f6575),

(0xd65012ba119a06b213663d0e03ffa0fc78617ff31fecbe66d1125f838edc374b,
 0x1ffaa3957681f395c8d646d056036fe3f8709dfc5dbc60ea153b5633df231451),

(0xe199ce4278c00f04f3dec53f72e13cf4e1bc6b10200b84e7a183f1a4f713705f,
 0x29e1ca2ff8749e18bd715370bd657c4d29dbbdfb43986061cb221525c16def8e),

(0x6b57d3dce59f1b03c7158f93564119900f952b611d0c1ed676f05adb41289ccc,
 0xb236ddf8440286f7c83592501624a1fb4a397d03225aa30ba2d05ba28807e465),

(0x1a4eb41ec5147dc9fce761bf87637123c27814ecd9975080b9de2b92b9e54e82,
 0x0dadb7940d9342d0738b719445960ab50999f303e82dd43c77d5de6b64278464),

(0x114913a67b61c4ee5177deaeecf1138a7ddfbe9dc36a1028d85dc95ee9b94a68,
 0x6f37abaac11439fd6394370eb746c26bee24024d06e142a3fb81bc28af63ce9a),

(0x9a6a44095380b935ad41761197f91e99ecb9a9777557de2d51e34dda32e47844,
 0x09cd0e8099a4341f9521bb0fd610edefc4bbe185ac864e2d8d6b1b8b58eea7b3),

(0x0bba2fac88d029643d8557b9850255c516d1851bad36184718ae3e416a5362b3,
 0xdddac00e5bf9d3ec2d5d472756cc3ee38b229118eafb9aacc2466d7673fe4ed4),

(0x32c52d265ab09d061ec9f035ef61a1af5a83ea92104ae9d2ddf9f99904155d53,
 0xb247a17ba9e07c128af69c824553cb0ea3bf4bbe994955bce2923cc7ee098d17),
]

# On pourra ensuite chercher dans notre lookup table quel point (obtenus après transformation char à char du FLAG) correspond à quelle lettre
flag = ""
for x, y in points:
    flag += lookup[(x, y)]

print(flag)
```


## Flag

<details>

<summary>Spoiler</summary>

```
FCSC{Dl0G_S0lV1ng!}
```
</details>
