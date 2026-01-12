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

### Try to exploit

Dans ce niveau, un attaquant peut changer le contenu de la variable `changeme` en débordant le tampon `buffer`. En cas réelle ceci permet à l'utilisateur de notre programme de modifier une variable de notre code.
Par exemple 
```
python3 -c 'print("Attack"*15)' | ./stack-zero
```

### Remediation

La mesure corrective consiste à utiliser des fonctions sécurisées, comme `fgets`, qui limitent le nombre de caractères lus, empêchant ainsi le dépassement de tampon.

### Find CWE Linked to the Weakness

CWE-126 : Buffer Over-read

### 3.2 Stack One

#### Identify the Weakness

- Utilisation de strcpy sans contrôle de taille sur un buffer local de 64 octets.

- changeme se trouve juste après le buffer dans la pile, donc il est écrasé quand on dépasse 64 octets.

#### Try to Exploit the Weakness

- Envoyer 64 caractères de remplissage + la valeur 0x496c5962 en little-endian.

- Soluce  : ./stack-one $(python -c 'print "A"*64 + "\x62\x59\x6c\x49"')

#### Find CWE Linked to the Weakness

- CWE associé : CWE-121: Stack-based Buffer Overflow.

#### Remediation

- Remplacer strcpy par une version bornée, par exemple strncpy(locals.buffer, argv[1], sizeof(locals.buffer)-1) + ajout d’un \0 final

Voici le **Markdown complet pour Stack Two**, suivant ton canevas :

***

### 3.3 Stack Two

#### Identify the Weakness

*   Utilisation de `strcpy` sans contrôle de taille sur un buffer local de 64 octets.
*   La variable `changeme` se trouve juste après le buffer dans la pile, donc elle est écrasée si on dépasse 64 octets.

#### Try to Exploit the Weakness

*   Envoyer 64 caractères de remplissage + la valeur `0x0d0a090a`
```bash
export ExploitEducation=$(python3 -c 'print("A"*64 + "\x0a\x09\x0a\x0d")')
/opt/phoenix/amd64/stack-two
```

#### Find CWE Linked to the Weakness

*   CWE associé : **CWE-121: Stack-based Buffer Overflow**  
    <https://cwe.mitre.org/data/definitions/121.html>

#### Remediation

*   Remplacer `strcpy` par une version sécurisée, par exemple :

```c
strncpy(locals.buffer, ptr, sizeof(locals.buffer)-1);
locals.buffer[sizeof(locals.buffer)-1] = '\0';
```

*   Ou vérifier la longueur avant la copie.


### 3.4 Stack Zero

#### Identify the Weakness

#### Try to Exploit the Weakness

#### Find CWE Linked to the Weakness

#### Remediation
