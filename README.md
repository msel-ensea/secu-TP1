# secu-TP1

## Template 

For each level, for your report:

- identify the weakness
- try to exploit the weakness
- find CWE linked to the weakness 
- propose a remediation.

## Level00

### Identify the Weakness

Le système contient un binaire SUID appartenant à l’utilisateur *flag00*, situé dans un répertoire caché.  
Ce fichier possède le bit SUID activé, ce qui signifie qu’il s’exécute avec les privilèges de *flag00*, même lorsqu’il est lancé par un autre utilisateur.

### Try to Understand the Impact

Un utilisateur comme *level00* peut exécuter ce programme et se retrouver avec les permissions de *flag00*.  
Cela représente un risque important : un attaquant pourrait utiliser ce binaire pour obtenir des droits supplémentaires et revenir dans le système avec les privilèges de *flag00*.

### Remediation

La mesure corrective la plus simple consiste à supprimer ce binaire SUID non nécessaire et à réinitialiser les identifiants de connexion de l’utilisateur *flag00* afin d’éviter tout abus futur.

### Find CWE Linked to the Weakness

CWE-250 : Execution with Unnecessary Privileges

## Level01

### Identify the Weakness

Le binaire SUID `/home/flag01/flag01` appelle la commande echo.  
Il dépend donc de la variable d’environnement PATH, contrôlable par l’utilisateur appelant.

### Try to Exploit the Weakness

L’attaquant place un faux binaire echo dans un répertoire contrôlé (ex. /tmp) et modifie PATH pour que ce faux binaire soit exécuté en priorité.  
Comme le programme est SUID, le code s’exécute avec les privilèges de *flag01*, permettant l’obtention d’un shell privilégié.

### Remediation

Utiliser des chemins d'accès absolus pour les exécutables critiques afin de prévenir les manipulations de la variable d'environnement PATH.

### Find CWE Linked to the Weakness

CWE-426: Untrusted Search Path

## Level02

### Identify the Weakness

Le binaire SUID `/home/flag02/level02` appelle `system` avec la commande construite à partir de `getenv("USER")`, qui est contrôlable par l'utilisateur.

### Try to Exploit the Weakness

Un attaquant peut définir la variable `USER` sur `; getflag`, permettant l'exécution de commandes arbitraires.  
Cela peut être fait comme suit :

```bash
USER='; getflag' ./level02
```

### Remediation

Sanitiser les entrées utilisateur et ne pas utiliser `system` avec des commandes basées sur des variables d'environnement non sécurisées.

### Find CWE Linked to the Weakness

CWE-426: Untrusted Search Path

## Level03

### Identify the Weakness

La faiblesse provient d’un cron job exécutant un script utilisable par des utilisateurs non privilégiés.  
Le script lancé par cron appartient à *flag03* mais exécute des programmes pouvant appartenir à n'importe quel utilisateur.

### Try to Exploit the Weakness

L’exploitation consiste à :
- Identifier le script exécuté par cron.
- Vérifier qu’il est utilisable par l’utilisateur *level03*.
- Donner une commande malveillante (`/bin/bash`).
- Attendre l’exécution automatique du cron job.
- Obtenir un shell avec les privilèges de *flag03*.
- Récupérer le flag avec `getflag`.

### Remediation

Restreindre l'accès aux scripts exécutés par cron afin qu'ils ne puissent être lancés que par leurs propriétaires ou des utilisateurs autorisés.

### Find CWE Linked to the Weakness

- CWE-732 – Incorrect Permission Assignment for Critical Resource

## Level04

### Identify the Weakness

Le binaire restreint l'accès aux fichiers, interdisant explicitement la lecture du fichier `token` en vérifiant si le nom de fichier contient "token".

### Try to Exploit the Weakness

L'attaquant peut créer un alias pour le fichier `token`, permettant ainsi à l'application de contourner la restriction.  
En utilisant la commande `su`, il peut changer d'utilisateur et ainsi accéder à ce fichier protégé :

```bash
ln -s ../../home/flag04/token my_key  # se place dans tmp
../../home/flag04/flag04 my_key
su flag04
```

### Remediation

Utiliser des contrôles d'accès stricts pour limiter l'accès aux fichiers sensibles dans l'application.

### Find CWE Linked to the Weakness

CWE-22: Improper Limitation of a Pathname to a Restricted Directory

## Level05

### Identify the Weakness

Clé SSH privée exposée et stockée dans un répertoire de sauvegarde lisible par tous (`/home/flag05/.backup/.ssh/id_rsa`), permettant à un utilisateur non autorisé de s'authentifier en tant qu'un autre utilisateur (*flag05*).

### Try to Exploit the Weakness

Exploiter la clé privée exposée pour se connecter directement en tant que *flag05* sans mot de passe.

Étapes :
1. Extraire la sauvegarde :
   ```bash
   tar xzvf .backup/backup-19072011.tgz -C /home/level05/
   ```
2. Ajuster les permissions :
   ```bash
   chmod 600 /home/level05/.backup/.ssh/id_rsa
   ```
3. Se connecter en utilisant la clé SSH :
   ```bash
   ssh -i /home/level05/.backup/.ssh/id_rsa flag05@localhost
   ```

Une fois connecté, exécutez :
```bash
getflag
```

### Remediation

Protéger les clés privées en restreignant l'accès aux répertoires où elles sont stockées et en appliquant des permissions strictes.

### Find CWE Linked to the Weakness

- CWE-522 – Identifiants insuffisamment protégés
- CWE-312 – Stockage en texte clair d’informations sensibles
- CWE-276 – Permissions par défaut incorrectes

## Level07
