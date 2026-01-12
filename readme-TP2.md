# secu-TP2

## Template 

For each level, for your report:

    identify the weakness
    try to exploit the weakness
    find CWE linked to the weakness (https://cwe.mitre.org)
    propose a remediation.
## Stack 

### 3.1 Stack Zero
### Identify the Weakness

Le niveau introduit le concept que la mémoire peut être accessible en dehors de sa région allouée. Les variables sur la pile sont disposées de manière contiguë, ce qui signifie que modifier la mémoire en dehors de la zone allouée peut changer l'exécution du programme.

### Try to Understand the Impact

Dans ce niveau, un attaquant peut changer le contenu de la variable `changeme` en débordant le tampon `buffer`. Si l'attaquant réussit à effectuer cette modification, il recevra un message de succès, indiquant qu'il a réussi à manipuler l'exécution du programme. En cas réelle ceci permet à l'utilisateur de notre programme de modifier une variable de notre code.

### Remediation

La mesure corrective consiste à utiliser des fonctions sécurisées, comme `fgets`, qui limitent le nombre de caractères lus, empêchant ainsi le dépassement de tampon.

### Find CWE Linked to the Weakness

CWE-126 : Buffer Over-read
