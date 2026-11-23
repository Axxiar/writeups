---
title: Shrimp WAF
tags:
  - web 
  - intro
  - CTF
  - 🇫🇷
---


- Author: Worty

## Description

La shrimp garde un secret qu’il faut absolument récupérer.

## Solution

### Quick overview

On a une interface web qui nous donne gentillement le code php de la page : 

```php
 <?php
if(isset($_GET["source"])) {
    die(highlight_file(__FILE__));
}

if (isset($_SERVER['QUERY_STRING']) &&
    stripos($_SERVER['QUERY_STRING'], 'shrimp_flag') !== false) {
    die('Blocked by Shrimp WAF 1.0');
}

if(isset($_GET["shrimp_flag"]) && $_GET["shrimp_flag"] === "Bye ShrimpWAF") {
    die(getenv("FLAG"));
}
?>
<!DOCTYPE HTML>
<head>
    <title>Shrimp WAF</title>
</head>
<body>
    <p style="text-align: center;">Shrimp WAF</p>
    <h4 style="text-align: center;">Want the flag? Fool the shrimp.</h4>
    <h5 style="text-align: center;">Click <a href="/?source=1">here</a> to check Shrimp WAF source code.</h5>
</body>
1
```

On sait maintenant comment les requêtes sont traitées, essayons de la manipuler pour atteindre la ligne `die(getenv("FLAG"));` afin d'obtenir le flag. 

Il semble qu'il faille simplement un paramètre de requête `?shrimp_flag="Bye ShrimpWAF"`, seulement une condition avant dans le code bloque les requêtes contenant "shrimp_flag" (`stripos($_SERVER['QUERY_STRING'], 'shrimp_flag') !== false)`)

### Bypasser le """WAF""" 🦐

En lisant de la [doc PHP sur la variable `$_GET`](https://www.php.net/manual/en/reserved.variables.get.php), on apprend ceci :

>  The values in $_GET are automatically passed through urldecode().

Ca voudrait dire qu'on pourrait utiliser l'encodage url pour passer le premier check en remplaçant un caractère par son équivalent encodé, et lors du second check l'appel à `urldecode` nous permettrait de le valider.

Testons cela en remplaçant `_` par `%5f` dans la requête : `.../?shrimp%5fflag=Bye%20ShrimpWAF` <br>
Bingo !

## Flag

<details> 
<summary>Spoiler</summary>

```
FCSC{20b8fc9d0aae508ec29a9084745a38fd87c07bf484af239afb22509e99fdf2f8}
```

</details>
