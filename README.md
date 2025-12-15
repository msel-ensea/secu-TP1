# secu-TP1

## Template 

For each level, for your report:

    identify the weakness
    try to exploit the weakness
    find CWE linked to the weakness (https://cwe.mitre.org)
    propose a remediation.

## Level00
    Le flag ici se trouve dans un dossier caché -rwsr-x--- 1 flag00 level00 7358 Nov 20  2011 /bin/.../flag00
    pour le trouver on peut faire la commande find / -type f -perm -u+s -exec ls -l {} \; 2>/dev/null | grep flag qui va rechercher tous les fichiers avec le bit setuit activé et qui va faire la recherche de celui s'appelant flag00

    Un programme setuid s'exécute avec les droits de l'utilisateur qui en est le propriétaire, même si c'est un autre utilisateur qui l'exécute. Un attaquant peut s'en servir pour exécuter un programme de même nom.

## Level01

