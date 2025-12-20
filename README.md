# secu-TP1

## Template 

For each level, for your report:

    identify the weakness
    try to exploit the weakness
    find CWE linked to the weakness (https://cwe.mitre.org)
    propose a remediation.

## Level00

### identify the weakness

Le système contient un binaire SUID appartenant à l’utilisateur *flag00*, situé dans un répertoire caché.  
Ce fichier possède le bit SUID activé, ce qui signifie qu’il s’exécute avec les privilèges de *flag00*, même lorsqu’il est lancé par un autre utilisateur.

### try to understand the impact

Un utilisateur comme *level00* peut exécuter ce programme et se retrouver avec les permissions de *flag00*.  
Cela représente un risque important : un attaquant pourrait utiliser ce binaire pour obtenir des droits supplémentaires et revenir dans le système avec les privilèges de *flag00*.

### remediation

La mesure corrective la plus simple consiste à supprimer ce binaire SUID non nécessaire et à réinitialiser les identifiants de connexion de l’utilisateur *flag00* afin d’éviter tout abus futur.

### find CWE linked to the weakness

CWE-250 : Execution with Unnecessary Privileges

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

### identify the weakness

Clé SSH privée exposée et stockée dans un répertoire de sauvegarde lisible par tous (/home/flag05/.backup/.ssh/id_rsa), permettant à un utilisateur non autorisé de s'authentifier en tant qu'un autre utilisateur (flag05).

### Try to exploit the weakness

Exploiter la clé privée exposée pour se connecter directement en tant que flag05 sans mot de passe.

Étapes :

tar xzvf .backup.tgz /home/level05/
chmod 600 .ssh/tmp/id_rsa
ssh -i .ssh/id_rsa flag05@localhost

Une fois connecté :

getflag

### Find CWE linked to the weakness

- CWE-522 – Identifiants insuffisamment protégés
- CWE-312 – Stockage en texte clair d’informations sensibles
- CWE-276 – Permissions par défaut incorrectes

## Level07

