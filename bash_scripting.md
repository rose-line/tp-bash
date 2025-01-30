# Les bases du scripting BASH

Un script BASH est un petit programme basé sur les commandes Linux. Vous pouvez mettre une suite de commandes ligne par ligne dans un script pour pouvoir exécuter cette ensemble de commandes, à la suite, en exécutant juste le script.

Mais les scripts sont encore plus puissants, ils fournissent des fonctionnalités de langages de programmation : les variables, déjà évoquées, mais aussi les instructions conditionnelles et les répétitions.

D'autres langages de scripting sont très populaires, notamment :

- PERL : très puissant dans la gestion de chaînes de caractères ;
- Python : un langage de programmation très structuré et de plus en plus populaire, y compris pour le scripting ;
- Tous les shells autres que BASH (zsh, csh...) fournissent un langage de scripting.

Cependant le scripting BASH reste extrêmement populaire dans le monde Linux car il est disponible immédiatement dans la quasi totalité des distributions Linux, et il est aussi très utilisé par les distributions elles-mêmes.

Ce document introduit les fondamentaux de la programmation BASH, et vous permettra déjà d'écrire des scripts intéressants. Vous retrouverez plus de détails dans le document pointé dans le README ou sur les nombreuses sources Internet sur le sujet.

## Écrire et exécuter un script BASH

Toutes les commandes que vous connaissez peuvent être utilisées dans un script. On écrit les scripts en utilisant un éditeur de fichier quelconque. Voici par exemple un script complet :

```bash
#!/bin/bash

cd /home
ls -l /home > /root/snapshot_reps_utilisateurs
du -s /home/* >> /root/snapshot_reps_utilisateurs
date >> /root/snapshot_reps_utilisateurs
```

Notez la première ligne : elle est nécessaire pour que le système sache qu'il s'agit d'un script BASH. Toute autre ligne commençant par `#` sera considérée comme un commentaire par BASH.

Puis on a juste une séquence de commandes, exécutées à la suite, comme si on les tapaient sur le terminal.

Après avoir sauvegardé le script (la convention est souvent de donner une extension `.sh`, par exemple `mon_script.sh`), il faut donner au fichier la permission d'exécution :

`chmod u+x mon_script.sh`

Un `ls` nous montrera que le fichier a bien les droits d'exécution pour le propriétaire (`x`).

Pour exécuter le script, il faut indiquer le chemin vers le script. S'il est dans le répertoire courant, il faut explicitement l'indiquer avec `./` (c'est parce que le répertoire courant n'est pas dans le PATH) :

`$ ./mon_script.sh`

## Arguments récupérés depuis la commande de lancement du script

Des variables spéciales sont créées lors de l'exécution d'un script. Par exemple, on peut récupérer les arguments passés au script par l'intermédiaire de variables `$1, $2, $3...` Si on lance le script avec :

`$ ./mon_script.sh pgahide 30`

alors, dans le script, la variable `$1` vaudra `pgahide` et la variable `$2` vaudra `30`.

De plus, on peut récupérer tous les arguments d'un coup avec la variable `$@` : `pgahide 30`

## Instructions conditionnelles

Voici la structure générale d'un `if/elseif/else` en BASH :

```bash
if [ cond ]
then
  instructions
elif [ cond ]
then
  instructions
else
  instructions
fi
```

Bien sûr, les `elif` et `else` ne sont pas obligatoires, on peut juste avoir un `if/else` classique ou bien un simple `if`. En revanche, un `if` se termine toujours par un `fi`. Et il ne faut pas oublier les *alors* (`then`). Les espaces autour des crochets de la conditions sont également nécessaires.

Voici un exemple :

```bash
#!/bin/bash
#exemple_if.sh

plat=$1
if [ "$plat" = "frites" ]
then
  echo "Wallah c'est trop bon !"
elif [ "$plat" = "fromage_de_chevre" ]
then
  echo "Non merci."
else
  echo "Je sais pas c'est quoi."
fi
```

Ce script affiche un message en fonction de ce que l'utilisateur a donné en argument.

## Entrées utilisateur

En plus de récupérer les arguments passés par l'utilisateur, on peut aussi prendre une entrée utilisateur pendant l'exécution grâce à l'instruction `read mon_entree`, qui va stocker dans la variable ici nommée `mon_entree` ce que l'utilisateur tape au clavier à ce moment.

```bash
#!/bin/bash
#nom.sh

echo "Entrez votre nom"
read nom
if [ -n "$nom" ]
then
  echo "Merci, $nom !"
else
  echo "Oh ! Vous n'avez rien écrit !"
fi
```

L'option `-n` utilisée ici vérifie si la string `$nom` n'est pas vide.

## Comparaison d'entiers

Pour les comparaisons numériques, utilisez les options suivantes (par exemple entre les crochets dans un `if` : `[ $val -gt 10 ]` => « est-ce que $val est supérieur à 10 ? ») :

- `-eq` : égal à
- `-ne` : différent de
- `-gt` : > (*greater than*)
- `-lt` : < (*lesser than*)
- `-ge` : >= (*greater or equl*)
- `-le` : <= (*lesser or equal*)

## Test sur les fichiers

Déterminer des informations sur les fichiers et répertoires avec les options suivantes :

- `-s` : vrai si le fichier n'est pas vide
- `-d` : le fichier spécifié est-il un répertoire ?
- `-f` : le fichier spécifié est-il un fichier ordinaire ?
- `-r` : vrai si le fichier existe ET qu'il est accessible en lecture par l'utilisateur courant
- `-w` : idem pour l'écriture
- `-x` : idem pour l'exécution

Par exemple :

```bash
if [ -s mon_fichier.txt ]
then
  cat mon_fichier.txt
```

## Répétitions

### Boucle `while`

Le script suivant demande un code postal de 5 chiffres à l'utilisateur et recommence tant que (*while*) le CP entré n'est pas au bon format. Il utilise un *pipe* pour passer le CP à `egrep`, qui est comme `grep` mais spécialisé dans les expressions régulières. L'expression régulière donnée `^[0-9]{5}$` ne reconnaît que les strings contenant 5 chiffres exactement. L'option `-v` permet d'inverser le résultat. Donc, si on trouve les 5 chiffres exactement, on renvoit false (et on sort du `while`), et sinon on renvoit true (et on rentre dans le `while`).

```bash
echo "Entrez un code postal (5 chiffres) : "
read CP

while echo $CP | egrep -v "^[0-9]{5}$" > /dev/null 2>&1
do
  echo "Vous devez entrer un code postal valide : 5 chiffres"
  echo "Entrez un code postal (5chiffres) : "
  read CP
done

echo "Merci !"
```

`> /dev/null` permet d'envoyer la sortie de `egrep` « à la poubelle ».  `2>&1` permet de rediriger la sortie d'erreurs (canal `2`) vers la sortie standard (canal `1` qui a déjà été redirigé vers la « poubelle »). Aucun affichage sur le terminal n'est donc permis pour cette commande : on ne souhaite pas que ces affichages viennent polluer l'interaction avec l'utilisateur de notre script. `> /dev/null 2>&1` est donc très utilisé pour éviter tout affichage d'une commande sur le terminal.

### Boucle `for`

On utilise souvent le `for` sur une liste d'éléments :

```bash
for personne in papa maman papi mamie
do
  useradd $personne
done
```

Notez l'absence du `$` dans l'instruction `for`.

### Sortie de boucle

L'instruction `break` permet de sortir à n'importe quel moment d'une boucle (par exemple quand un `if` à déterminé que le boulot de la boucle était terminé).

### Instruction `case`

Pareil qu'une suite de `if/elif`, mais plus lisible :

```bash
nom="patrice"
case $nom in
  patrice) echo "C'est Patrice";;
  papa) echo "C'est papa";;
  *) echo "Personne inconnue"
esac
```

Notez les doubles points-virgules, le cas par défaut (`*)`) et le `esac` qui termine. On peut également utiliser un OU avec `|` :

```bash
read reponse
case $reponse in
  o|oui) echo "d'accord !";;
  n|no|non) echo "tant pis !";;
  *) echo "Réponse incorrecte";;
esac
```

## Substitution de commandes

On peut exécuter une commande dont le résultat va être utilisé dans une autre commande directement avec la substitution de commande `$(commande)`. Cet exemple met la date courante dans la variable `maintenant` : `maintenant=$(date)`
