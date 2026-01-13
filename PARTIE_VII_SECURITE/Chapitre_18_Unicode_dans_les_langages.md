# Chapitre 18 — Unicode dans les langages

## Introduction

Chaque langage de programmation gère Unicode différemment, créant des pièges subtils pour les développeurs travaillant avec du texte international. Comprendre ces différences est crucial pour écrire du code robuste et portable.

Ce chapitre explore comment les langages populaires gèrent Unicode et leurs pièges courants.

## C / C++

### Gestion historique

**C89/C99 :**
- **Aucune gestion Unicode native**
- **char = octet** (typiquement 8 bits)
- **Chaînes = tableaux d'octets**
- **Encodage dépendant du système**

**Problèmes :**
```c
// Code buggé (C89)
char* text = "café";  // Encodage système (souvent Latin-1)
printf("%s\n", text); // Affiche correctement si terminal compatible

// Mais :
strlen(text);  // Nombre d'octets, pas de caractères
// "café" = 5 octets en UTF-8, 4 en Latin-1
```

### C++ moderne

**C++11+ :**
- **char16_t/char32_t** : Types Unicode explicites
- **std::u16string/std::u32string** : Chaînes Unicode
- **Codecs Unicode** : Conversions standardisées

**Bon usage :**
```cpp
#include <string>
#include <codecvt>
#include <locale>

// UTF-8 string
std::string utf8_text = u8"café";

// Conversion UTF-8 → UTF-16
std::u16string utf16 = std::wstring_convert<std::codecvt_utf8_utf16<char16_t>, char16_t>{}.from_bytes(utf8_text);

// Comptage de caractères (pas d'octets)
size_t char_count = utf16.length();  // Correct pour BMP
```

### Problèmes courants

**1. Confusion char vs wchar_t :**
```cpp
// Windows
sizeof(wchar_t) == 2;  // UTF-16

// Linux
sizeof(wchar_t) == 4;  // UTF-32

// Code non portable !
```

**2. Conversions manuelles :**
```cpp
// DANGER : conversion maison
char* utf8_to_latin1(const char* utf8) {
    // Code buggé, perte de données
    return (char*)utf8;  // Cast dangereux
}
```

**3. API Windows :**
```cpp
// Windows : fonctions A/W
MessageBoxA(NULL, "Hello", "Title", MB_OK);     // ANSI
MessageBoxW(NULL, L"Hello", L"Title", MB_OK);   // Wide (UTF-16)

// Bonne pratique
#ifdef UNICODE
#define MessageBox MessageBoxW
#else
#define MessageBox MessageBoxA
#endif
```

### Solutions modernes

**Bibliothèques recommandées :**
- **ICU** : Référence pour Unicode en C/C++
- **UTF8-CPP** : Header-only pour UTF-8
- **Boost.Locale** : Wrappers modernes

**Exemple avec ICU :**
```cpp
#include <unicode/unistr.h>
#include <unicode/ustream.h>

void processUnicodeText() {
    // Chaîne Unicode robuste
    UnicodeString text = UnicodeString::fromUTF8("café");
    
    // Longueur en caractères (pas octets)
    int32_t length = text.length();
    
    // Normalisation
    UnicodeString normalized = text.normalize(Normalizer::NFC);
    
    // Conversion vers d'autres encodages
    std::string utf8;
    text.toUTF8String(utf8);
}
```

## Java

### Design Unicode natif

**Java depuis 1.0 :**
- **char = 16 bits** (UTF-16 code unit)
- **String = séquence UTF-16**
- **Support Unicode complet**

**Architecture :**
```java
String text = "café";
int codeUnits = text.length();        // 4 (UTF-16 units)
int codePoints = text.codePointCount(0, text.length()); // 4

// Accès par code point
int firstChar = text.codePointAt(0);  // 'c' = 99
```

### Problèmes des surrogates

**UTF-16 et émoji :**
```java
String emoji = "😀";  // U+1F600
char[] chars = emoji.toCharArray();
// chars = ['\uD83D', '\uDE00'] (surrogates !)

System.out.println(emoji.length());  // 2 (piège !)
System.out.println(emoji.codePointCount(0, emoji.length())); // 1 (correct)
```

**Comparaisons dangereuses :**
```java
String text1 = "café";      // NFC
String text2 = "café";      // NFD

text1.equals(text2);        // false !
text1.equalsIgnoreCase(text2); // false !

// Solution
Normalizer.normalize(text1, Normalizer.Form.NFC)
         .equals(Normalizer.normalize(text2, Normalizer.Form.NFC));
```

### Bonnes pratiques Java

**Utiliser les bonnes méthodes :**
```java
public class UnicodeUtils {
    // Comptage correct
    public static int countGraphemes(String text) {
        BreakIterator boundary = BreakIterator.getCharacterInstance();
        boundary.setText(text);
        int count = 0;
        while (boundary.next() != BreakIterator.DONE) {
            count++;
        }
        return count;
    }
    
    // Validation UTF-8
    public static boolean isValidUTF8(byte[] bytes) {
        try {
            new String(bytes, StandardCharsets.UTF_8);
            return true;
        } catch (Exception e) {
            return false;
        }
    }
}
```

## JavaScript

### UTF-16 historique

**JavaScript/ECMAScript :**
- **String = UTF-16 code units**
- **charCodeAt()** : Retourne code unit (0-65535)
- **fromCharCode()** : Crée depuis code units

**Pièges classiques :**
```javascript
let text = "😀";
console.log(text.length);        // 2 (surrogates)
console.log(text.charCodeAt(0)); // 55357 (high surrogate)
console.log(text.charCodeAt(1)); // 56832 (low surrogate)

console.log(text === "😀");      // true
console.log(text.charCodeAt(0) === "😀".charCodeAt(0)); // true
```

### ES6+ améliorations

**Nouvelles fonctionnalités :**
```javascript
// Code point correct
let emoji = "😀";
console.log(emoji.codePointAt(0)); // 128512 (U+1F600)

// Création depuis code point
let heart = String.fromCodePoint(0x2764); // ❤️

// Spread operator pour grapheme clusters
let graphemes = [..."👨‍👩‍👧‍👦"]; // ["👨‍👩‍👧‍👦"]
console.log(graphemes.length);    // 1 (correct !)
```

### Normalisation

**Support intégré :**
```javascript
let text1 = "café";  // NFC
let text2 = "café";  // NFD

text1 === text2;     // false

// Normalisation
text1.normalize() === text2.normalize(); // true (NFC par défaut)
text1.normalize('NFKC') === text2.normalize('NFKC'); // true
```

### Problèmes cross-browser

**Différences historiques :**
- **IE11 et anciens** : Support limité des surrogates
- **Navigateurs modernes** : Support complet
- **Node.js** : UTF-8 natif mais strings UTF-16

**Solution :**
```javascript
function isModernBrowser() {
    return typeof String.prototype.normalize === 'function' &&
           typeof String.fromCodePoint === 'function';
}
```

## Python

### Évolution progressive

**Python 2 :**
- **str = octets** (encodage dépendant)
- **unicode = objets Unicode** (UTF-16/32)
- **Confusion majeure**

**Python 3 :**
- **str = texte Unicode** (UTF-8 logique)
- **bytes = données binaires**
- **Clarté totale**

### Python 3 : simplicité

**Gestion native :**
```python
# UTF-8 par défaut
text = "café"
len(text)        # 4 (caractères)
text.encode()    # b'café' (UTF-8)
text[0]          # 'c' (premier caractère)

# Comptage correct
import unicodedata
len(text)        # 4 (toujours correct en Python 3)
```

### Normalisation

**Support complet :**
```python
import unicodedata

text1 = "café"  # NFC
text2 = "café"  # NFD

text1 == text2  # False

# Normalisation
unicodedata.normalize('NFC', text1) == unicodedata.normalize('NFC', text2)  # True
```

### Problèmes résiduels

**Encodage des fichiers source :**
```python
# -*- coding: utf-8 -*-
# Nécessaire si caractères non-ASCII dans le code source
```

**Performance :**
```python
# len() est O(1) mais peut être trompeur avec combinaisons
text = "e\u0301"  # e + accent (2 code points)
len(text)         # 2
unicodedata.normalize('NFC', text)  # "é" (1 code point)
```

## Pièges classiques

### 1. Longueur de chaîne

**Erreur commune :**
```python
# Tous ces langages
text = "👨‍👩‍👧‍👦"
print(len(text))  # Varie selon le langage !
# JavaScript: 11, Java: 7, Python: 7, C++: dépend
```

**Solution universelle :**
```javascript
// JavaScript
[...text].length
```
```java
// Java
text.codePointCount(0, text.length())
```
```python
# Python
len(text)  # Correct en Python 3
```

### 2. Itération de caractères

**Problème :**
```cpp
// C++ : itération octet par octet
std::string utf8 = "café";
for (char c : utf8) {
    std::cout << c << std::endl;  // Octets individuels !
}
// Output: c a f Ã ©
```

**Solution :**
```cpp
// Utiliser ICU ou bibliothèques Unicode
UnicodeString uText = UnicodeString::fromUTF8(utf8);
for (int32_t i = 0; i < uText.length(); i++) {
    UChar32 c = uText.char32At(i);
    // c contient le code point correct
}
```

### 3. Comparaisons de chaînes

**Sans normalisation :**
```java
"café".equals("café")  // false dans tous les langages
```

**Avec normalisation :**
```java
// Java
Normalizer.normalize("café", Normalizer.Form.NFC)
         .equals(Normalizer.normalize("café", Normalizer.Form.NFC))
```

### 4. Stockage en base de données

**Problème :**
```sql
-- Base configurée en UTF-8
INSERT INTO users (name) VALUES ('café');  -- NFC
INSERT INTO users (name) VALUES ('café');  -- NFD

SELECT * FROM users WHERE name = 'café';   -- Ne trouve qu'un résultat
```

**Solutions :**
- Normaliser à l'entrée
- Utiliser collation Unicode-aware
- Index fonctionnel sur forme normalisée

## Recommandations générales

### Choisir le bon langage

**Pour Unicode simple :**
- **Python 3** : Le plus intuitif
- **Java** : Bon support, mais surrogates complexes
- **JavaScript ES6+** : Amélioré récemment

**Pour Unicode avancé :**
- **C/C++ avec ICU** : Puissance maximale
- **Java avec ICU4J** : Écosystème riche
- **.NET** : Framework Unicode complet

### Tests systématiques

**Suite de tests Unicode :**
```python
def test_unicode_handling(language_implementation):
    test_cases = [
        "café",           # Latin avec accents
        "北京",           # Chinois
        "русский",        # Cyrillique
        "👨‍👩‍👧‍👦",     # Émoji composé
        "e\u0301",        # Caractères combinés
    ]
    
    for text in test_cases:
        # Tester encodage/décodage
        # Tester normalisation
        # Tester comptage
        # Tester itération
        pass
```

### Outils de développement

**Validateurs Unicode :**
- **UnicodeChecker** (en ligne)
- **ICU tools** (command line)
- **Browser dev tools** (console)

**Bibliothèques cross-langage :**
- **ICU** : Référence universelle
- **Unicode libraries** : Par langage
- **Testing frameworks** : Unicode-aware

Comprendre les particularités Unicode de chaque langage est essentiel pour éviter les bugs subtils et créer des applications véritablement internationales.