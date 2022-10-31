# Raycaster

> reverse

Ce défi contient 5 flags.

Author: [Alexandre-Xavier Labonté-Lamoureux (AXDOOMER)](https://github.com/axdoomer)

J'ai trouvé ce jeu sur Internet et il fait parfois des choses bizarres. Je ne sais pas... c'est étrange. On dirait qu'il n'y a pas d'objectifs de jeu, mais je ne peux pas m'empêcher de passer des heures à parcourir le labyrinthe. J'ai passé des heures à jouer et je suis en retard dans mes travaux scolaires.

Contrôles:
* `Flèches`: Bouger et tourner
* `Shift`: Courir
* `Page up` et `Page down`: Regarder en haut et en bas
* `Escape`: Fermer le jeu

--------------

Défi 1 (première rencontre) : Il y a un paramètre de ligne de commande qui est analysé juste en haut de la fonction `main_main` (la fonction `main()` de Go s'il s'agissait de code Java ou C).

Vous devez utiliser IDA Free 7.6, qui dispose d'un décompilateur cloud. Il fait généralement du bon travail pour décompiler les binaires Go. Bien sûr, vous pouvez également essayer d'autres outils si cela vous aide. 

Vous avez besoin de Linux pour exécuter ce défi. Ce défi a été testé sur Ubuntu 20.04.3 LTS. Vous devez installer SDL2 (`apt install libsdl2-2.0-0`). Faites savoir au concepteur du défi si vous rencontrez des difficultés pour l'exécuter. Si vous avez besoin d'aide pour utiliser IDA, il existe de nombreux didacticiels en ligne sur des sites Web tels que Youtube. Notez qu'IDA Free fonctionne sous Linux, Mac et Windows. La clé de décompilation est F5.

--------------

Défi 2 (texture manquante) : Il y a une texture qui n'est jamais affichée nulle part dans le labyrinthe, bien qu'elle soit dans le binaire du jeu. Pouvez-vous l'extraire? Peut-être pourriez-vous même demander au jeu de l'afficher.

Le fichier binaire de ce défi est le même que le précédent.

--------------

Défi 3 (code de triche) : Il y a un code de triche dans ce jeu. C'est quelque chose que vous tapez sur le clavier. Pouvez-vous trouver ce que c'est? Qu'est ce que ça fait?

Ce drapeau ne commence pas par `flag-`. Vous devez entrer le code de triche comme flag.

Le fichier binaire de ce défi est le même que le précédent.

--------------

Défi 4 (porte dérobée) : Il semble que ce jeu soit une porte dérobée ([backdoor](https://fr.wikipedia.org/wiki/Porte_d%C3%A9rob%C3%A9e)). Elle ne semble pas activée par défaut. Pouvez-vous trouver quel est le but de cette porte dérobée? Qu'est ce que ça fait?

Le fichier binaire de ce défi est le même que le précédent.

--------------

Défi 5 (la pièce) : Il y a une pièce inaccessible sur la carte. Pouvez-vous entrer à l'intérieur? Peut-être avez-vous besoin de créer vos propres hacks.

Assurez-vous que vous exécutez le binaire du jeu depuis votre terminal. Il se peut qu'il y imprime quelque chose une fois que vous êtes entré.

Le fichier binaire de ce défi est le même que le précédent.

## Setup

Requirements:
- Une distribution basée sur Linux

# Writeup

Challenge 1:

Dans le haut de la fonction `main_main`, on voit `os_Args`. Le code indique une comparaison avec `0x6C6965766E752D2DLL`. On peut convertir cela en `char`, on obtient `lievnu--` qui est une string à l'envers. On peut voir deux arrays qui se XOR une et l'autre puis un appel à `fmt_Fprintln`. Si on exécute le binaire avec `--unveil`, un flag apparaîtra au terminal. `flag-notsosecret`

Challenge 2:

Dans la fonction `main_main`, on peut voir le quatrième appel à `main_textureDecoder`. Celui-ci reçoit une grosse string Base64. Si on la décode, on obtient une image PNG. Elle contient le texte `flag-mi55ingt3xtur3`. 

Challenge 3:

Dans la fonction `main_main`, on peut voir `main_circular_buffer` à partir duquel on crée une slice comparée à `ykoops` avec `runtime_memequal`. Les strings sont à l'envers, donc en fait c'est le mot `spooky`. Si on l'entre dans le jeu, cela donne bien un résultat qui confirme que c'est le bon flag. 

Challenge 4:

En fouillant dans le code, on trouve une fonction `main_updateSpecials` qui effectue une commande `os_exec_Command`. Cela correspond à une backdoor. Au début de la fonction, on voit la string ``jvvrq8--rcqvg`kl,amo-pcu-z3wdvpae``. On voit un XOR avec le nombre 2. Si on effectue cette opération sur la string, on obtient un URL pastebin qu'on peut accéder à `https://pastebin.com/raw/x1uftrcg`. La page contient ``oehn$jfdy{fd`zlm``. Si on continue à regarder le code, on voit plus loin un XOR avec le nombre 9. On fait donc le XOR sur le contenu de la page. Cela donne `flag-compromised`. 

Challenge 5:

Il y a une salle inaccessible sur la carte du jeu qu'on voit en haut à gauche lorsqu'on joue. Il faut aller dedans. La fonction qui vérifie si on est à l'intérieur s'appelle `main_updateTics`. Dedans, on peut voir la variable `main__stmp_5` qui contient `D4DACCC2E0EECAE856567CEEE8E0E0D8` suivi de `F8C6FC6CCAAACE56E6CAEC56D2D6CE54`. Si on concatène les deux ensemble pour obtenir `F8C6FC6CCAAACE56E6CAEC56D2D6CE54D4DACCC2E0EECAE856567CEEE8E0E0D8`, il faut ensuite rotate les bits vers la droite par 1 pour obtenir l'inverse de l'opération `v23[i] = __ROL8__((unsigned __int8)v21[i + 7], 63);` vue dans le code. Ensuite, le caractère `B` (66) est ajouté. On a donc `lpptw>++tewpafmj*gki+ves+gUe6~c|B`. Juste après, on XOR par le nombre 4 pour obtenir une string renversée. Il s'agit de l'URL d'où est téléchargé le flag `flag-haveYouSeenTheMovie?` pour être affiché. 

Si on voulait le faire sans reverse la fonction `main_updateTics`, il faudrait modifier au moins deux des quatre `if` qui commencent par `if ( !main_worldmap` pour que le code sous leur scope s'exécute toujours. Dans le code assembleur, on peut les voir sous la forme suivante: 

```
.text:00000000006850D5 84 DB                                   test    bl, bl
.text:00000000006850D7 75 09                                   jnz     short loc_6850E2
.text:00000000006850D9 F2 0F 11 84 24 48 01 00+                movsd   [rsp+2B8h+var_170], xmm0
.text:00000000006850D9 00
.text:00000000006850E2
.text:00000000006850E2                         loc_6850E2:                             ; CODE XREF: main_main+A47↑j



.text:0000000000685134 84 DB                                   test    bl, bl
.text:0000000000685136 75 09                                   jnz     short loc_685141
.text:0000000000685138 F2 0F 11 84 24 50 01 00+                movsd   qword ptr [rsp+2B8h+var_168], xmm0
.text:0000000000685138 00
.text:0000000000685141
.text:0000000000685141                         loc_685141:                             ; CODE XREF: main_main+AA6↑j
```

Il y en a quatre, les deux montrés ci-dessus sont seulement pour la flèche pour "avancer". La collision marchera quand même quand on recule. Il faut patcher les instructions `jnz` (0x75 0x09) et les écraser avec des instructions `nop` (0x90). Le tour est joué! On peut passer à travers les murs.


