# Chapitre 14 — Locale, langue et encodage

## Introduction

Le concept de **locale** représente l'une des interfaces les plus importantes entre les applications et le système d'exploitation. Une locale définit comment le système doit présenter les informations culturelles : format des dates, des nombres, de la monnaie, et bien sûr, les règles linguistiques pour le texte.

Ce chapitre explore comment les locales influencent le traitement du texte et des encodages dans les systèmes modernes.

## Qu'est-ce qu'une locale ?

### Définition formelle

Une locale est un ensemble de paramètres définissant les **conventions culturelles et linguistiques** d'une région ou d'une langue spécifique.

**Composants d'une locale :**
- **Langue** : Code de langue (fr, en, de, ja, etc.)
- **Pays/Région** : Code de pays (FR, US, DE, JP, etc.)
- **Variante** : Variantes culturelles spécifiques
- **Charset** : Encodage de caractères associé

### Format standard POSIX

**Notation :** `language[_territory][.codeset][@modifier]`

**Exemples :**
```
fr_FR.UTF-8    # Français de France, UTF-8
en_US.UTF-8    # Anglais américain, UTF-8
de_DE@euro     # Allemand allemand avec euro
zh_CN.gb18030  # Chinois simplifié, GB18030
ja_JP.eucjp    # Japonais, EUC-JP
```

### Variables d'environnement

**Sous Unix/Linux :**
```bash
export LANG=fr_FR.UTF-8
export LC_ALL=fr_FR.UTF-8
export LC_CTYPE=fr_FR.UTF-8      # Classification des caractères
export LC_COLLATE=fr_FR.UTF-8    # Tri et comparaison
export LC_MONETARY=fr_FR.UTF-8   # Formats monétaires
export LC_NUMERIC=fr_FR.UTF-8    # Formats numériques
export LC_TIME=fr_FR.UTF-8       # Formats de date/heure
export LC_MESSAGES=fr_FR.UTF-8   # Messages système
```

**LC_ALL** surcharge toutes les autres variables LC_*.

## Classification des caractères

### Fonctions isalpha(), isdigit(), etc.

Les fonctions de classification des caractères (`isalpha`, `isdigit`, `isspace`, etc.) dépendent de la locale :

**Locale C (ASCII) :**
```c
isalpha('é')  // 0 (faux)
isalpha('a')  // 1 (vrai)
isspace(' ')  // 1 (vrai)
isspace('　') // 0 (faux - espace idéographique)
```

**Locale française :**
```c
setlocale(LC_CTYPE, "fr_FR.UTF-8");
isalpha('é')  // 1 (vrai - lettre française)
isalpha('a')  // 1 (vrai)
isspace(' ')  // 1 (vrai)
isspace('　') // 1 (vrai - espace idéographique)
```

### Tableaux de caractères

Chaque locale définit des tableaux internes pour :
- **Lettres majuscules/minuscules**
- **Chiffres décimaux**
- **Caractères d'espacement**
- **Caractères de ponctuation**

**Exemple en C :**
```c
#include <locale.h>
#include <ctype.h>

int main() {
    setlocale(LC_ALL, "fr_FR.UTF-8");

    // Maintenant isalpha() reconnaît les caractères accentués
    printf("%d\n", isalpha('é'));  // 1
    printf("%d\n", isalpha('à'));  // 1

    return 0;
}
```

## Tri et collation (LC_COLLATE)

### Problème du tri ASCII

**Tri ASCII simple :**
```c
strcmp("café", "cafe")  // 'é' > 'e' (233 > 101)
Résultat : cafe, café (ordre incorrect)
```

**Tri correct :**
```c
strcoll("café", "cafe")  // Considère 'é' comme 'e' + accent
Résultat : cafe, café (ordre linguistique)
```

### Niveaux de collation

La collation Unicode définit **4 niveaux de comparaison** :

1. **Niveau primaire** : Différences de base (a ≠ b)
2. **Niveau secondaire** : Diacritiques (a ≠ á)
3. **Niveau tertiaire** : Casse (a ≠ A)
4. **Niveau quaternaire** : Autres différences (espaces, ponctuation)

**Exemple :**
```
Niveau 1 : a = a (même lettre)
Niveau 2 : a ≠ á (différents diacritiques)
Niveau 3 : a ≠ A (différente casse)
```

### Locale et collation

**En français :**
```c
setlocale(LC_COLLATE, "fr_FR.UTF-8");
// Maintenant strcoll() respecte l'ordre français
strcoll("café", "cafe");  // café vient après cafe
```

**Comparaisons linguistiques :**
- **cote** < **côte** < **coté** < **côté**
- **café** et **cafe** sont équivalents au niveau primaire

## Formats régionaux

### Dates et heures (LC_TIME)

**Locale américaine :**
```c
setlocale(LC_TIME, "en_US.UTF-8");
// Format: MM/DD/YYYY
strftime(buf, sizeof(buf), "%x", time);  // 12/31/2025
```

**Locale française :**
```c
setlocale(LC_TIME, "fr_FR.UTF-8");
// Format: DD/MM/YYYY
strftime(buf, sizeof(buf), "%x", time);  // 31/12/2025
```

### Nombres et monnaies

**LC_NUMERIC :**
```c
setlocale(LC_NUMERIC, "fr_FR.UTF-8");
// Séparateur décimal: virgule
printf("%'f\n", 1234.56);  // 1 234,56

setlocale(LC_NUMERIC, "en_US.UTF-8");
// Séparateur décimal: point
printf("%'f\n", 1234.56);  // 1,234.56
```

**LC_MONETARY :**
```c
setlocale(LC_MONETARY, "fr_FR.UTF-8");
// Format monétaire français
strfmon(buf, sizeof(buf), "%n", 1234.56);  // 1 234,56 €

setlocale(LC_MONETARY, "en_US.UTF-8");
// Format monétaire américain
strfmon(buf, sizeof(buf), "%n", 1234.56);  // $1,234.56
```

## ICU (International Components for Unicode)

### Limites des locales système

Les locales POSIX ont des limitations :
- **Support limité** : Pas toutes les langues
- **Versions anciennes** : Données Unicode obsolètes
- **Incohérences** : Comportement différent selon OS

### ICU comme solution

**ICU fournit :**
- **Collation avancée** : Support Unicode complet
- **Normalisation** : NFC, NFD, NFKC, NFKD
- **Formatage** : Dates, nombres, monnaies
- **Recherche** : Break iterators, recherche avancée

**Exemple de collation ICU :**
```cpp
#include <unicode/ucol.h>

UErrorCode status = U_ZERO_ERROR;
UCollator* coll = ucol_open("fr_FR", &status);

UCharIterator iter1, iter2;
// Configuration pour collation française
ucol_setStrength(coll, UCOL_SECONDARY);  // Ignore casse et accents

int result = ucol_strcollUTF8(coll, "café", -1, "cafe", -1, &status);
```

### Avantages d'ICU

**Cross-platform** : Même comportement partout
**Unicode complet** : Support de toutes les langues
**Performant** : Optimisé pour la production
**Extensible** : Nouvelles locales ajoutables

## Problèmes courants

### Locale non définie

**Problème :**
```c
// Locale C par défaut (ASCII seulement)
isalpha('é');  // 0 (faux)
```

**Solution :**
```c
setlocale(LC_ALL, "");  // Utilise la locale système
// ou
setlocale(LC_ALL, "fr_FR.UTF-8");  // Définit explicitement
```

### Incohérences entre OS

**Windows vs Linux :**
```c
// Linux: fr_FR.UTF-8 fonctionne
setlocale(LC_ALL, "fr_FR.UTF-8");

// Windows: nécessite des noms différents
setlocale(LC_ALL, "French_France.65001");  // UTF-8 sous Windows
```

### Applications multithreads

**Attention :** `setlocale()` affecte tout le processus !

**Solutions :**
- **ICU** : Locale par objet, thread-safe
- **Boost.Locale** : Wrappers C++ modernes
- **Configuration explicite** : Éviter les changements dynamiques

## Bonnes pratiques

### Configuration d'application

```c
// Configuration robuste
const char* locale = getenv("LANG");
if (!locale) locale = "C.UTF-8";  // Fallback sécurisé

if (setlocale(LC_ALL, locale) == NULL) {
    fprintf(stderr, "Locale invalide: %s\n", locale);
    setlocale(LC_ALL, "C");  // Fallback minimal
}
```

### Test de locales

**Tests essentiels :**
```bash
# Tester différentes locales
export LANG=fr_FR.UTF-8
./mon_programme

export LANG=en_US.UTF-8
./mon_programme

export LANG=C
./mon_programme
```

### Documentation

**Pour les utilisateurs :**
```text
Configuration recommandée:
export LANG=fr_FR.UTF-8
export LC_ALL=fr_FR.UTF-8
```

**Pour les développeurs :**
```c
/**
 * Cette fonction nécessite une locale UTF-8 configurée
 * pour fonctionner correctement avec les caractères Unicode.
 */
```

Les locales constituent le lien crucial entre les applications et l'environnement linguistique du système, permettant une internationalisation correcte des programmes.