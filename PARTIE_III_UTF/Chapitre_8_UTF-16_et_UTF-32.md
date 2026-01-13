# Chapitre 8 — UTF-16 et UTF-32

## Introduction

Bien que UTF-8 domine le web et les systèmes de fichiers, UTF-16 et UTF-32 restent importants dans certains contextes techniques. Comprendre leurs mécanismes et cas d'usage permet de choisir l'encodage approprié pour chaque situation.

Ce chapitre explore les encodages alternatifs à UTF-8 et leurs compromis spécifiques.

## UTF-16 et surrogates

### Architecture de base

UTF-16 utilise une approche **hybride** :
- **Code points BMP :** 2 octets directs
- **Code points SMP :** 4 octets (surrogates)

**Avantages :**
- Efficace pour texte asiatique (CJK souvent en BMP)
- Bon compromis mémoire/performance

### Mécanisme des surrogates

**Principe :**
- **High surrogate :** U+D800-U+DBFF (1,024 valeurs)
- **Low surrogate :** U+DC00-U+DFFF (1,024 valeurs)
- **Combinaison :** 1,024 × 1,024 = 1,048,576 code points supplémentaires

**Calcul du code point :**
```
Code point = (H - 0xD800) × 0x400 + (L - 0xDC00) + 0x10000
```

### Exemples concrets

**'A' (U+0041) :**
```
Direct : 0041 (2 octets)
```

**'世' (U+4E16) :**
```
Direct : 4E16 (2 octets)
```

**'😀' (U+1F600) :**
```
High : D83D
Low  : DE00
Résultat : D83D DE00 (4 octets)
```

### Endianness en UTF-16

**Problème :** Ordre des octets
- **Big-endian :** D83D DE00
- **Little-endian :** 3DD8 00DE

**Solutions :**
- **BOM (Byte Order Mark) :** U+FEFF au début
- **Contexte :** UTF-16BE / UTF-16LE explicite

## UTF-32 : simplicité vs coût

### Principe fondamental

UTF-32 utilise **4 octets fixes** par code point :
- **Avantage :** Simplicité absolue
- **Inconvénient :** Consommation mémoire élevée

**Structure :**
```
U+0041 (A) : 00000000 00000000 00000000 01000001
U+1F600 (😀) : 00000000 00011111 01100000 00000000
```

### Comparaison de taille

**Texte "Hello 😀" :**

| Encodage | Taille | Efficacité |
|----------|--------|------------|
| UTF-8    | 11 octets | 100%      |
| UTF-16   | 14 octets | 79%       |
| UTF-32   | 28 octets | 39%       |

### Avantages techniques

**Simplicité de programmation :**
```c
// UTF-32 : accès direct par index
uint32_t* text_utf32 = L"Hello 😀";
uint32_t char_count = wcslen(text_utf32); // Correct
uint32_t third_char = text_utf32[2];      // Direct
```

**VS UTF-8 :**
```c
// UTF-8 : calcul complexe
char* text_utf8 = "Hello 😀";
int byte_len = strlen(text_utf8);     // 11
int char_count = utf8_char_count(text_utf8); // 7
// Accès au 3ème caractère : parsing nécessaire
```

## Utilisation dans les OS et langages

### Windows : UTF-16 historique

**Architecture interne :**
- **Kernel :** UTF-16 pour toutes les chaînes
- **APIs :** Fonctions W (wide) utilisent UTF-16
- **Fichiers :** UTF-8 depuis Windows 10 1903

**Exemple :**
```cpp
// Windows API
WCHAR* wide_string = L"Hello World";  // UTF-16
WriteFile(hFile, wide_string, ...);
```

**Migration progressive :**
- **Ancien :** UTF-16 partout
- **Moderne :** Conversions UTF-8 ↔ UTF-16 aux frontières

### Java : UTF-16 natif

**Design historique :**
- **Java 1.0 :** Unicode 1.1.5 (BMP uniquement)
- **String :** UTF-16 interne depuis toujours
- **char :** 16 bits (hérité)

**Conséquences :**
```java
String text = "Hello 😀";
int length = text.length();        // 7 (code units)
int codePoints = text.codePointCount(0, text.length()); // 7
```

**Problème des surrogates :**
```java
// Avec émoji
String emoji = "😀";
char[] chars = emoji.toCharArray();
// chars = ['\uD83D', '\uDE00'] (surrogates séparés)
```

### JavaScript : UTF-16 par défaut

**Historique :**
- **ECMAScript 1 :** Strings en UTF-16
- **charCodeAt() :** Retourne des code units (16 bits)

**Problèmes courants :**
```javascript
let text = "😀";
console.log(text.length);        // 2 (surrogates)
console.log(text.charCodeAt(0)); // 55357 (high surrogate)
console.log(text.charCodeAt(1)); // 56832 (low surrogate)
console.log(text.codePointAt(0)); // 128512 (code point correct)
```

### .NET : UTF-16 natif

**Framework .NET :**
- **string :** UTF-16 interne
- **char :** 16 bits
- **Encodage par défaut :** UTF-8 pour fichiers

**Gestion moderne :**
```csharp
string text = "Hello 😀";
int length = text.Length;          // 7 (code units)
int runes = text.EnumerateRunes().Count(); // 7 (code points)
```

### Bases de données

**Oracle :** Historiquement UTF-16
**SQL Server :** Support NCHAR (UTF-16)
**Avantages :** Calculs de longueur simples

## Comparaison UTF-8 / UTF-16 / UTF-32

### Tableau comparatif

| Critère | UTF-8 | UTF-16 | UTF-32 |
|---------|-------|--------|--------|
| **Taille ASCII** | 1 octet | 2 octets | 4 octets |
| **Taille CJK** | 3 octets | 2 octets | 4 octets |
| **Taille émoji** | 4 octets | 4 octets | 4 octets |
| **Simplicité parsing** | Complexe | Moyen | Simple |
| **Mémoire texte latin** | Optimal | Gâchis | Très gâchis |
| **Mémoire texte asiatique** | Moyen | Optimal | Gâchis |
| **Robustesse corruption** | Excellente | Moyenne | Bonne |
| **Compatibilité legacy** | Parfaite | Faible | Aucune |

### Cas d'usage optimaux

**UTF-8 :**
- Web, APIs REST
- Systèmes Unix/Linux
- Stockage fichiers
- Communication réseau

**UTF-16 :**
- Windows interne
- Java applications
- JavaScript moteurs
- Texte majoritairement CJK

**UTF-32 :**
- Traitement algorithmique
- Recherche rapide
- APIs nécessitant accès direct
- Langages de programmation interne

## Cas pratiques

### Migration Windows UTF-16 → UTF-8

**Problème historique :**
```cpp
// Ancien code Windows
WCHAR buffer[256];
GetWindowTextW(hwnd, buffer, 256); // UTF-16
// Stockage : conversion nécessaire
```

**Solution moderne :**
```cpp
// Windows 10+
char buffer[256];
GetWindowTextA(hwnd, buffer, 256); // UTF-8 direct
```

### JavaScript : gestion des surrogates

**Problème :**
```javascript
function countChars(text) {
  return text.length; // Bug avec émoji !
}
countChars("😀"); // Retourne 2 au lieu de 1
```

**Solution :**
```javascript
function countChars(text) {
  return [...text].length; // Spread operator compte les code points
}
countChars("😀"); // Retourne 1 ✓
```

### Choix d'encodage en production

**API REST :**
```json
{
  "encoding": "utf-8",
  "content": "Hello 世界"
}
```

**Base de données :**
```sql
-- MySQL
CREATE TABLE users (
  name VARCHAR(255) CHARACTER SET utf8mb4
);

-- PostgreSQL
CREATE TABLE users (
  name TEXT -- UTF-8 par défaut
);
```

**Cache mémoire :**
```java
// Java : String (UTF-16) pour manipulation
// Cache : byte[] (UTF-8) pour économie
```

Comprendre ces encodages permet de choisir la solution optimale pour chaque contexte technique, évitant les pièges de performance et de compatibilité.