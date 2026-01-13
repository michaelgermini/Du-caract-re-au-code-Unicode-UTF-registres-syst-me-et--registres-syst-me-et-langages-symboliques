# Chapitre 16 — Texte et OS modernes

## Introduction

Les systèmes d'exploitation modernes ont évolué considérablement dans leur gestion du texte et des encodages. De simples gestionnaires de fichiers ASCII, ils sont devenus des environnements multilingues sophistiqués supportant Unicode complet.

Ce chapitre explore comment les OS contemporains gèrent le texte international.

## Linux et UTF-8

### Adoption native d'UTF-8

**Historique :**
- **1990s-2000s** : Adoption progressive d'UTF-8
- **2008** : UTF-8 devient défaut dans la plupart des distributions
- **Aujourd'hui** : Standard universel sous Linux

**Configuration :**
```bash
# Vérifier la locale
locale
# LANG=fr_FR.UTF-8
# LC_CTYPE=fr_FR.UTF-8

# Lister les locales disponibles
locale -a | grep UTF-8
```

### Architecture UTF-8

**Kernel Linux :**
- **UTF-8 natif** depuis le kernel 2.6
- **Filesystem** : Support UTF-8 dans les noms de fichiers
- **Console** : Terminal UTF-8 avec polices appropriées

**GNU libc (glibc) :**
- **Locales complètes** : Support de centaines de langues
- **ICU intégré** : Composant standard dans de nombreuses distributions
- **Iconv** : Conversions d'encodage robustes

### Gestion des noms de fichiers

**UTF-8 dans filesystem :**
```bash
# Créer fichier avec caractères Unicode
touch "café_文档.txt"

# Lister avec caractères spéciaux
ls -l | cat  # Préserve l'encodage

# Recherche avec Unicode
find . -name "*café*" -type f
```

**Problème historique :**
- Anciens systèmes de fichiers (FAT32, ext2 sans options)
- Migration nécessaire vers UTF-8

### Applications et outils

**Outils système :**
```bash
# Éditeur avec support Unicode
nano fichier.txt     # Support UTF-8 natif
vim fichier.txt      # Support complet Unicode

# Recherche texte
grep "café" *.txt    # Fonctionne avec UTF-8

# Tri linguistique
sort -k1 < fichier.txt  # Utilise LC_COLLATE
```

**Programmation :**
```python
# Python 3 : UTF-8 par défaut
with open('café.txt', 'w', encoding='utf-8') as f:
    f.write('Bonjour café !')

# Lecture automatique
with open('café.txt', 'r') as f:  # Détecte automatiquement UTF-8
    content = f.read()
```

## macOS et Unicode

### Héritage NeXT et évolution

**Historique :**
- **NeXTSTEP** : Unicode précoce (1990s)
- **macOS X (2001)** : UTF-8 natif
- **Aujourd'hui** : Support Unicode complet

**Architecture :**
- **CoreFoundation** : Framework Unicode de base
- **NSString** : Classes Objective-C avec Unicode
- **HFS+ → APFS** : Filesystems Unicode natifs

### Frameworks Unicode

**CoreFoundation :**
```objc
// NSString avec Unicode
NSString *text = @"café";
NSUInteger length = [text length];  // Nombre de caractères (5)
NSData *utf8 = [text dataUsingEncoding:NSUTF8StringEncoding];
```

**Foundation framework :**
```objc
// Comparaison linguistique
NSString *str1 = @"café";
NSString *str2 = @"cafe";
NSComparisonResult result = [str1 localizedCompare:str2];
// Considère les accents pour le tri
```

### Gestion des langues

**Préférences système :**
```bash
# Langues configurées
defaults read NSGlobalDomain AppleLanguages
# ("fr-FR", "en-US")

# Locale principale
defaults read NSGlobalDomain AppleLocale
# "fr_FR"
```

**Format régional :**
```objc
// Formatters localisés
NSNumberFormatter *formatter = [[NSNumberFormatter alloc] init];
[formatter setLocale:[NSLocale currentLocale]];
NSString *formatted = [formatter stringFromNumber:@1234.56];
// France: "1 234,56"
// US: "1,234.56"
```

## Android, iOS

### Android : Java + Linux

**Architecture :**
- **Kernel Linux** : UTF-8 natif
- **Java VM** : Strings UTF-16
- **Framework** : Support Unicode complet

**Gestion du texte :**
```java
// Java sur Android
String text = "café";
int length = text.length();        // 5 (code units UTF-16)
int codePoints = text.codePointCount(0, text.length()); // 4

// Conversion UTF-8 pour stockage
byte[] utf8 = text.getBytes(StandardCharsets.UTF_8);
```

**Locales système :**
```java
// Locale actuelle
Locale current = Locale.getDefault();

// Configuration utilisateur
Configuration config = getResources().getConfiguration();
LocaleList locales = config.getLocales();
```

### iOS : Objective-C/Swift + Darwin

**Foundation framework :**
```swift
// Swift moderne
let text = "café"
let length = text.count  // 4 (grapheme clusters)

// NSString (Objective-C)
let nsString = text as NSString
let utf8Length = nsString.lengthOfBytes(using: .utf8)
```

**Support linguistique :**
```swift
// Détection de langue
let recognizer = NLLanguageRecognizer()
recognizer.processString(text)
let language = recognizer.dominantLanguage
// "fr" pour français
```

## Problèmes inter-plateformes

### Incohérences de comportement

**Tri de texte :**
```python
# Linux (glibc)
import locale
locale.setlocale(locale.LC_COLLATE, 'fr_FR.UTF-8')
sorted(['café', 'cafe'])  # ['cafe', 'café']

# macOS (ICU)
# Comportement légèrement différent selon version ICU
```

**Normalisation :**
```javascript
// JavaScript (tous navigateurs)
'café'.normalize('NFC')  // Comportement identique

// Mais différences selon OS sous-jacent
```

### Encodage des noms de fichiers

**Windows → Linux :**
```bash
# Problème : noms de fichiers Windows (UTF-16) → Linux (UTF-8)
# Solution : conversion explicite
convmv -f utf-16 -t utf-8 --notest fichier_windows
```

**macOS → Linux :**
```bash
# NFD (macOS) → NFC (Linux) pour compatibilité
python3 -c "
import unicodedata
import os
for file in os.listdir('.'):
    normalized = unicodedata.normalize('NFC', file)
    if normalized != file:
        os.rename(file, normalized)
"
```

### Applications cross-platform

**Stratégies :**
1. **Stocker en UTF-8** : Standard universel
2. **Normaliser à l'entrée** : NFC recommandé
3. **Utiliser ICU** : Comportement identique partout
4. **Tester sur toutes plateformes** : CI/CD multi-OS

### Synchronisation cloud

**Problèmes courants :**
- **Dropbox, Google Drive** : Conversions automatiques
- **Git** : Peut corrompre l'encodage si mal configuré
- **Email** : Conversions MIME selon clients

**Configuration Git :**
```bash
# Configurer Git pour UTF-8
git config --global core.quotepath false  # Préserve caractères Unicode
git config --global i18n.commitEncoding utf-8
git config --global i18n.logOutputEncoding utf-8
```

## Bonnes pratiques modernes

### Configuration développeur

**Linux :**
```bash
# .bashrc
export LANG=fr_FR.UTF-8
export LC_ALL=fr_FR.UTF-8
export EDITOR='code --wait'  # VSCode avec UTF-8
```

**macOS :**
```bash
# Variables d'environnement
export LANG=fr_FR.UTF-8
export LC_ALL=fr_FR.UTF-8
```

**Windows :**
```cmd
REM Variables système
set LANG=fr_FR.UTF-8
set PYTHONUTF8=1  REM Python UTF-8
```

### Outils de développement

**Éditeurs modernes :**
- **VSCode** : UTF-8 par défaut, support Unicode complet
- **IntelliJ** : Configuration UTF-8 automatique
- **Vim/Neovim** : Support Unicode natif

**Terminaux :**
```bash
# Windows Terminal (moderne)
# Support UTF-8, émoji, ligatures

# Alacritty, Kitty (Linux/macOS)
# Terminaux modernes avec Unicode complet
```

### Tests d'internationalisation

**Tests essentiels :**
```python
def test_unicode_handling():
    # Test caractères multilingues
    texts = ["café", "北京", "русский", "🌟🚀"]
    
    for text in texts:
        # Test encodage/décodage
        utf8 = text.encode('utf-8')
        decoded = utf8.decode('utf-8')
        assert decoded == text
        
        # Test normalisation
        nfc = unicodedata.normalize('NFC', text)
        nfd = unicodedata.normalize('NFD', text)
        # Vérifier réversibilité
        assert unicodedata.normalize('NFC', nfd) == nfc
```

### Monitoring production

**Logs et métriques :**
- **Taux d'erreurs d'encodage**
- **Performance des conversions**
- **Utilisation des locales**
- **Problèmes de caractères corrompus**

**Alertes :**
```python
# Détection de corruption UTF-8
import chardet

def detect_corruption(text_bytes):
    result = chardet.detect(text_bytes)
    if result['encoding'] != 'utf-8' and result['confidence'] > 0.9:
        alert_admin(f"Corruption détectée: {result}")
```

Les systèmes d'exploitation modernes offrent un support Unicode sophistiqué, mais nécessitent une compréhension approfondie pour éviter les pièges d'internationalisation.