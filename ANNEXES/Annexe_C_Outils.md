# Annexe C — Outils

## Outils de développement

### Bibliothèques Unicode

#### ICU (International Components for Unicode)

**Usage :** Bibliothèque de référence pour Unicode

**Langages :** C/C++, Java, .NET

**Fonctionnalités :**
- Normalisation (NFC, NFD, NFKC, NFKD)
- Collations (tri linguistique)
- Conversions d'encodage
- Formatage dates/nombres
- Recherche texte avancée

**Exemple C++ :**
```cpp
#include <unicode/unistr.h>
#include <unicode/normlzr.h>

UErrorCode status = U_ZERO_ERROR;
UnicodeString text = UnicodeString::fromUTF8("café");
UnicodeString normalized = text.normalize(Normalizer::NFC, status);
```

#### Python

**Builtin :**
```python
# Normalisation
text = "café"
normalized = text.normalize('NFC')

# Comptage grapheme clusters
import regex
count = len(regex.findall(r'\X', text))
```

**Libraries externes :**
- **ftfy :** Réparation automatique d'encodage
- **unicodedata2 :** Données Unicode étendues

#### JavaScript

**Builtin (ES6+) :**
```javascript
// Normalisation
const text = "café".normalize('NFC');

// Conversion
const encoder = new TextEncoder();
const decoder = new TextDecoder('utf-8');
const bytes = encoder.encode(text);
```

**Libraries :**
- **grapheme-splitter :** Comptage grapheme clusters
- **unicode-trie :** Structures de données Unicode

### Outils en ligne

#### Unicode Character Table

- **Site :** unicode-table.com
- **Usage :** Recherche de caractères par nom/code point
- **Fonctionnalités :** Copier caractères, voir propriétés

#### Unicode Code Charts

- **Site :** unicode.org/charts
- **Usage :** Spécifications officielles
- **Contenu :** Tous les blocs Unicode

#### Fileformat.info

- **Site :** fileformat.info
- **Usage :** Informations détaillées sur caractères
- **Fonctionnalités :** Encodages, propriétés, exemples

## Outils système

### Linux

**Commandes :**
```bash
# Détection d'encodage
file fichier.txt

# Conversion
iconv -f latin1 -t utf-8 fichier.txt > converti.txt

# Validation UTF-8
python3 -c "open('fichier.txt', encoding='utf-8').read()"

# Recherche caractères
grep -P '[\x{80}-\x{10FFFF}]' fichier.txt
```

**Locales :**
```bash
# Lister locales disponibles
locale -a

# Définir locale française
export LANG=fr_FR.UTF-8
```

### Windows

**Commandes :**
```cmd
REM Conversion avec PowerShell
powershell "Get-Content fichier.txt -Encoding Default | Out-File -Encoding UTF8 nouveau.txt"

REM Vérifier BOM
powershell "Get-Content fichier.txt -Encoding Byte -TotalCount 3"
```

**Configuration :**
```cmd
REM Code page console
chcp 65001  REM UTF-8
chcp 1252   REM Windows-1252
```

### macOS

**Commandes :**
```bash
# Conversion
iconv -f macintosh -t utf-8 fichier.txt

# Normalisation
echo "café" | python3 -c "import sys, unicodedata; print(unicodedata.normalize('NFC', sys.stdin.read().strip()))"
```

## Outils spécialisés

### Création de polices

#### FontForge

**Usage :** Éditeur de polices open source

**Fonctionnalités :**
- Création de glyphes personnalisés
- Support Unicode complet
- Export TrueType/OpenType

**Exemple workflow :**
1. Ouvrir police existante
2. Ajouter glyphes PUA
3. Définir mappages Unicode
4. Générer fichier .ttf/.otf

#### Glyphs

**Usage :** Éditeur commercial macOS

**Avantages :**
- Interface moderne
- Support OpenType avancé
- Intégration Adobe

### Validation et test

#### Unicode Validator

- **Site :** unicode.org/reports/tr39
- **Usage :** Validation conformité Unicode
- **Types :** Streams, fichiers, API

#### ICU Test Suite

- **Usage :** Tests exhaustifs ICU
- **Couverture :** Tous les aspects Unicode
- **Intégration :** CI/CD possible

### Analyse de texte

#### TextQL

- **Usage :** Requêtes SQL sur texte
- **Unicode-aware :** Comptage correct des caractères

#### UnicodeData.txt Parser

```python
# Parser maison pour UnicodeData.txt
import urllib.request

def load_unicode_data():
    url = "https://unicode.org/Public/UNIDATA/UnicodeData.txt"
    with urllib.request.urlopen(url) as response:
        for line in response:
            fields = line.decode('utf-8').strip().split(';')
            codepoint = int(fields[0], 16)
            name = fields[1]
            category = fields[2]
            # Traiter les données...
```

Ces outils permettent de travailler efficacement avec Unicode dans tous les environnements de développement.