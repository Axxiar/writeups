---
title: Transport ME
tags:
  - hardware
  - intro
  - CTF
  - 🇫🇷
---

- Author: ElyKar

## Description

Vous avez récupéré une carte de transport en commun, mais il ne reste que deux voyages dessus. Vous avez la possibilité d’interagir directement avec la carte. Pour obtenir le flag, vous devez réussir à voyager 5 fois.

Pour interagir avec la carte, veuillez vous connecter au service en ligne indiqué.

![carte](./transport-me.png)

## Solution

### Quick overview

En se connectant via netcat on a ces infos:

```
0. Informations sur la carte
1. Voyage
2. Lecture UID
3. Lecture d'un bloc
4. Ecriture d'un bloc
5. Quitter
>>> 0
Les cartes du modèle FCSC-64B4 possèdent un identifiant (UID) de 32 bits, ainsi que quatre blocs de 16 bits réinscriptibles.

Le modèle mémoire est le suivant:
+---------------+----------------+------------------------+
| Adresse bloc  |    Contenu     |      Description       |
+---------------+----------------+------------------------+
|             0 | valeur 16 bits | Bloc 0                 |
|             1 | valeur 16 bits | Bloc 1                 |
|             2 | valeur 16 bits | Bloc 2                 |
|             3 | valeur 16 bits | Bloc 3                 |
|             4 | UID[15:0]      | Octets 1 et 0 de l'UID |
|             5 | UID[31:16]     | Octets 3 et 2 de l'UID |
+---------------+----------------+------------------------+

Les commandes exposées par la carte permettent de lire et d'écrire un bloc, ou bien de lire l'UID.
```

On comprend vite qu'il faut identifier quel bloc écrire pour s'ajouter plus de voyages

### Réécrire le nombre de ticket

En utilisant la feature de lecture d'un bloc on inspecte le contenu de ceux de 0 a 3 (4 et 5 étant réservés pour l'UID).

On trouve ainsi que le bloc qui contient le nombre de ticket (et donc a overwrite) est le 1.

Reste à utiliser la fonctionnalité d’écriture d'un bloc pour y mettre autant de ticket que l'on souhaite.

## Flag

<details> 
<summary>Spoiler</summary>

```
FCSC{0519152bbd38a7a3c0fe98cbdb2f98d74a48598660718fe8b3695935ede5b37f}
```

</details>
