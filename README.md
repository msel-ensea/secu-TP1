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

