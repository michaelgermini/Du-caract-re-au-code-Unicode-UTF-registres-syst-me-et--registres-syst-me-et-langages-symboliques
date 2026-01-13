# Chapitre 7 — UTF-8 (le standard du web)

## Introduction

UTF-8 est l'encodage de caractères le plus utilisé au monde. Sa domination s'explique par des propriétés techniques exceptionnelles qui en font le choix idéal pour le web, les systèmes Unix, et la plupart des applications modernes.

Ce chapitre détaille le fonctionnement interne d'UTF-8 et explique pourquoi il a supplanté tous ses concurrents.

## Principe de base

### Encodage variable intelligent

UTF-8 utilise un système d'encodage **variable** où le nombre d'octets dépend de la valeur du code point :

**Règles de base :**
- **U+0000-U+007F :** 1 octet (ASCII compatible)
- **U+0080-U+07FF :** 2 octets
- **U+0800-U+FFFF :** 3 octets
- **U+10000-U+10FFFF :** 4 octets

### Structure des octets

**Premier octet :** Indique la longueur de la séquence
**Octets suivants :** Contiennent les bits de données

**Masques d'identification :**
```
1 octet  : 0xxxxxxx
2 octets : 110xxxxx 10xxxxxx
3 octets : 1110xxxx 10xxxxxx 10xxxxxx
4 octets : 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
```

## Encodage variable (1 à 4 octets)

### Calcul détaillé

**Pour un code point C :**

1. **Déterminer le nombre d'octets N**
2. **Extraire les bits de C**
3. **Les répartir dans N octets selon le masque**

### Exemples concrets

**'A' (U+0041) = 1 octet :**
```
Code point : 01000001
Masque     : 0xxxxxxx
Résultat   : 01000001 (0x41)
```

**'é' (U+00E9) = 2 octets :**
```
Code point : 0000000011101001
Masque     : 110xxxxx 10xxxxxx
Étapes     :
1. 11000011 10101001 (0xC3 0xA9)
2. Vérification : C3 = 11000011, A9 = 10101001 ✓
```

**'€' (U+20AC) = 3 octets :**
```
Code point : 0010000010101100
Masque     : 1110xxxx 10xxxxxx 10xxxxxx
Résultat   : 11100010 10000010 10101100 (0xE2 0x82 0xAC)
```

**'😀' (U+1F600) = 4 octets :**
```
Code point : 000000011111011000000000
Masque     : 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
Résultat   : 11110000 10011111 10011000 10000000 (0xF0 0x9F 0x98 0x80)
```

## Compatibilité ASCII

### Propriété fondamentale

**UTF-8 préserve parfaitement ASCII :**
- Tous les caractères ASCII (0x00-0x7F) s'encodent identiquement
- Les programmes ASCII existants fonctionnent sans modification

**Conséquence majeure :**
```c
// Code C historique fonctionne inchangé
char* text = "Hello World!";
printf("%s\n", text); // Fonctionne parfaitement
```

### Impact historique

Cette compatibilité explique le succès d'UTF-8 :
- **Migration facile :** Pas besoin de changer les logiciels existants
- **Robustesse :** Fichiers ASCII = fichiers UTF-8 valides
- **Interopérabilité :** ASCII comme sous-ensemble universel

## Exemples concrets

### Table de conversion complète

| Caractère | Code point | UTF-8 (hex) | UTF-8 (binaire) |
|-----------|------------|-------------|-----------------|
| A         | U+0041    | 41          | 01000001       |
| é         | U+00E9    | C3 A9       | 11000011 10101001 |
| α         | U+03B1    | CE B1       | 11001110 10110001 |
| 你       | U+4F60    | E4 BD A0    | 11100100 10111101 10100000 |
| 😀       | U+1F600   | F0 9F 98 80| 11110000 10011111 10011000 10000000 |

### Analyse d'un texte multi-langue

**Texte :** "Hello 世界 😀"

**Décomposition :**
```
H     : U+0048 → 48
e     : U+0065 → 65
l     : U+006C → 6C
l     : U+006C → 6C
o     : U+006F → 6F
[espace] : U+0020 → 20
世    : U+4E16 → E4 B8 96
界    : U+754C → E7 95 8C
[espace] : U+0020 → 20
😀    : U+1F600 → F0 9F 98 80
```

**Octets finaux :** `48 65 6C 6C 6F 20 E4 B8 96 E7 95 8C 20 F0 9F 98 80`

## Pourquoi UTF-8 a gagné

### Avantages techniques

1. **Compatibilité ASCII :** Héritage logiciel préservé
2. **Efficacité mémoire :** Optimal pour texte latin occidental
3. **Robustesse :** Résistant à la corruption (resynchronisation facile)
4. **Auto-détection :** Masques distinctifs permettent la détection

### Comparaison avec concurrents

**VS UTF-16 :**
- UTF-8 : Meilleur pour le web (bande passante)
- UTF-16 : Meilleur pour JavaScript (performance interne)

**VS UTF-32 :**
- UTF-8 : Économique en mémoire
- UTF-32 : Accès direct par index

### Adoption mondiale

**Statistiques d'usage (2024) :**
- **Web :** >95% des pages en UTF-8
- **Linux/Unix :** UTF-8 par défaut
- **macOS :** UTF-8 pour fichiers
- **Bases de données :** UTF-8mb4 (MySQL), UTF-8 (PostgreSQL)

### Cas particulier : Windows

Windows utilise historiquement UTF-16 en interne mais :
- **Fichiers texte :** UTF-8 avec BOM depuis Windows 10 1903
- **APIs :** Conversions UTF-16 ↔ UTF-8
- **Console :** Support UTF-8 amélioré

## Pièges courants avec UTF-8

### Erreurs de programmation

**Problème : Traiter UTF-8 comme ASCII**
```c
char* text = "café";  // UTF-8: 63 61 66 C3 A9
int len = strlen(text);  // Retourne 5, mais 4 caractères !
```

**Solution : Utiliser des fonctions Unicode-aware**
```c
// En C avec ICU
UChar32* utf32 = u_strToUTF32(...);
int32_t char_count = u_countChar32(...);
```

### Corruption de données

**Scénario : Troncature au milieu d'une séquence**
```
Texte original : café (63 61 66 C3 A9)
Tronqué à 4 octets : 63 61 66 C3 → caf� (invalide)
```

**Détection de corruption :**
- Octets isolés 10xxxxxx ou 11111xxx
- Séquences incomplètes
- Premier octet invalide

### Validation UTF-8

**Algorithme de validation :**
1. Vérifier le premier octet
2. Compter les octets de continuation attendus
3. Valider chaque octet de continuation (10xxxxxx)
4. Vérifier la valeur du code point final

**Outil pratique :**
```python
def is_valid_utf8(bytes_data):
    try:
        bytes_data.decode('utf-8')
        return True
    except UnicodeDecodeError:
        return False
```

UTF-8 représente l'équilibre parfait entre compatibilité, efficacité et universalité, expliquant sa domination totale dans l'informatique moderne.