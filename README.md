# secu-TP1

## Template 

For each level, for your report:

    identify the weakness
    try to exploit the weakness
    find CWE linked to the weakness (https://cwe.mitre.org)
    propose a remediation.

## Level00
- Le flag ici se trouve dans un dossier caché -rwsr-x--- 1 flag00 level00 7358 Nov 20  2011 /bin/.../flag00
- pour le trouver on peut faire la commande find / -type f -perm -u+s -exec ls -l {} \; 2>/dev/null | grep flag qui va rechercher tous les fichiers avec le bit setuit activé et qui va faire la recherche de celui s'appelant flag00
- Ce programme nous permet de nous connecter en tant que flagOO pourtant nous étions l'utilisateur level00. Un attaquant peut s'en servir pour revenir dans le système en tant que flag00 et donc bénéficier de ses droits.
- Ici la solution peut juste être de supprimer ce fichier et de changer les identifiant de connection de l'utilisateur flag00

## Level01

### identify the weakness

Le binaire SUID /home/flag01/flag01 appelle la commande echo.
Il dépend donc de la variable d’environnement PATH, contrôlable par l’utilisateur appelant.

### try to exploit the weakness

L’attaquant place un faux binaire echo dans un répertoire contrôlé (ex. /tmp) et modifie PATH pour que ce faux binaire soit exécuté en priorité.
Comme le programme est SUID, le code s’exécute avec les privilèges de flag01, permettant l’obtention d’un shell privilégié.

### find CWE linked to the weakness

CWE-426: Untrusted Search Path

## Level03

### Identify the weakness

La faiblesse provient d’un cron job exécutant un script utilisable par des utilisateurs non privilégiés.
Le script lancé par cron appartient à flag03 mais execute des programmes pouvant appartenir a n'importe quel utilisateur.

### Try to exploit the weakness

L’exploitation consiste à :

Identifier le script exécuté par cron
Vérifier qu’il est utilisable par l’utilisateur level03
Donner une commande malveillante (/bin/bash)

Attendre l’exécution automatique du cron job
Obtenir un shell avec les privilèges de flag03
Récupérer le flag avec getflag

### Find CWE linked to the weakness

La vulnérabilité correspond aux CWE suivantes :

- CWE-732 – Incorrect Permission Assignment for Critical Resource
- Le script critique est utilisable par des utilisateurs non autorisés

## Level05