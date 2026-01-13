# Chapitre 9 — BOM, endianess et pièges

## Introduction

Les encodages multi-octets introduisent des complexités absentes d'UTF-8 : l'ordre des octets (endianness) et la détection automatique. Ces problèmes ont donné naissance au Byte Order Mark (BOM) et à de nombreux pièges pratiques.

Ce chapitre explore ces subtilités techniques et leurs implications réelles en production.

## Byte Order Mark

### Définition et rôle

Le BOM est un **caractère spécial** (U+FEFF) placé au début des fichiers pour indiquer :
- L'encodage utilisé (UTF-8, UTF-16, UTF-32)
- L'ordre des octets (endianness)

**Valeurs du BOM :**

| Encodage | BOM (hex) | BOM (décimal) |
|----------|-----------|---------------|
| UTF-8    | EF BB BF  | 239 187 191  |
| UTF-16BE | FE FF     | 254 255      |
| UTF-16LE | FF FE     | 255 254      |
| UTF-32BE | 00 00 FE FF | 0 0 254 255 |
| UTF-32LE | FF FE 00 00 | 255 254 0 0 |

### Histoire et controverse

**Origines :**
- Créé pour UTF-16 (problème d'endianness)
- Étendu à UTF-8 et UTF-32
- Controversé pour UTF-8 (inutile mais répandu)

**Controverse UTF-8 BOM :**
- **Pour :** Détection automatique fiable
- **Contre :** Corruption des programmes ASCII, overhead

### Comportement des applications

**Avec BOM :**
- **Éditeurs :** Masquent généralement le BOM
- **Navigateurs :** L'ignorent (BOM ≠ contenu)
- ** Programmes :** Peuvent le traiter comme un caractère normal

**Exemple problématique :**
```php
// PHP : BOM cause "headers already sent"
<?php // Fichier commence par BOM
header('Content-Type: text/html'); // Erreur !
```

## Little endian / Big endian

### Concepts fondamentaux

**Endianness :** Ordre de stockage des octets en mémoire

**Little Endian (Intel x86) :**
```
Nombre 0x12345678
Mémoire : 78 56 34 12
```

**Big Endian (RISC, réseau) :**
```
Nombre 0x12345678
Mémoire : 12 34 56 78
```

### Impact sur les encodages

**UTF-16 Big Endian :**
```
'A' (U+0041) : 00 41
```

**UTF-16 Little Endian :**
```
'A' (U+0041) : 41 00
```

**UTF-32 Big Endian :**
```
'A' (U+0041) : 00 00 00 41
```

**UTF-32 Little Endian :**
```
'A' (U+0041) : 41 00 00 00
```

### Architectures courantes

**Little Endian :**
- Intel x86/x64
- ARM (mode little endian par défaut)
- Mobile devices

**Big Endian :**
- PowerPC (historique)
- SPARC (historique)
- Réseau (TCP/IP spécifie big endian)

## Problèmes réels en production

### Corruption UTF-16 sans BOM

**Scénario :**
1. Fichier créé sur Windows (little endian)
2. Ouvert sur Mac PowerPC (big endian)
3. Texte illisible

**Exemple concret :**
```
Texte original : "Hello" (UTF-16LE)
Bytes : 48 00 65 00 6C 00 6C 00 6F 00

Lu comme UTF-16BE : H e l l o (octets inversés)
Résultat : "䡥汬汬敯"
```

### BOM UTF-8 dans les scripts

**Problème PHP :**
```php
// fichier.php (avec BOM UTF-8)
<?php
echo "Hello World";
// Output: (BOM invisible)Hello World
// Mais HTTP headers corrompus
```

**Problème Python 2 :**
```python
# fichier.py (avec BOM UTF-8)
#!/usr/bin/env python
print("Hello")
# Erreur: invalid syntax (BOM interprété comme code)
```

### Confusion d'encodage

**Problème courant :**
- Fichier prétendument UTF-8
- Contient des séquences UTF-16
- Corruption silencieuse

**Détection :**
```bash
# Linux
file mon_fichier.txt
# Output: UTF-16 Unicode text, with BOM
```

### Problèmes de streaming

**HTTP sans BOM :**
```
Content-Type: text/plain; charset=utf-16
```
- Client suppose endianness
- Risque d'erreur selon plateforme

## Bonnes pratiques modernes

### Pour les fichiers texte

**UTF-8 avec BOM (recommandé pour Windows) :**
- **Avantages :** Détection automatique, compatibilité Notepad
- **Usage :** Fichiers de configuration, CSV, logs

**UTF-8 sans BOM (recommandé pour Unix) :**
- **Avantages :** Compatibilité scripts, pas de corruption
- **Usage :** Code source, documents web

### Pour les APIs et protocoles

**JSON (RFC 8259) :**
- **Encodage :** UTF-8 obligatoire
- **BOM :** Interdit (cause d'erreurs de parsing)

**XML :**
- **Déclaration :** `<?xml version="1.0" encoding="utf-8"?>`
- **BOM :** Optionnel mais problématique

**HTTP :**
```http
Content-Type: application/json; charset=utf-8
```
- Pas de BOM dans le body

### Outils et détection

**Détection automatique :**
```python
# Python
import chardet
with open('file.txt', 'rb') as f:
    raw = f.read()
    result = chardet.detect(raw)
    print(result)  # {'encoding': 'utf-8', 'confidence': 0.99}
```

**Normalisation :**
```bash
# Convertir en UTF-8 propre
iconv -f utf-16 -t utf-8 fichier_utf16.txt > fichier_utf8.txt
```

### Code défensif

**Fonction de lecture sécurisée :**
```python
def read_text_file(filepath):
    with open(filepath, 'rb') as f:
        raw = f.read()
    
    # Détecter BOM
    if raw.startswith(b'\xef\xbb\xbf'):      # UTF-8 BOM
        encoding = 'utf-8'
        content = raw[3:]
    elif raw.startswith(b'\xff\xfe'):        # UTF-16LE BOM
        encoding = 'utf-16'
        content = raw[2:]
    elif raw.startswith(b'\xfe\xff'):        # UTF-16BE BOM
        encoding = 'utf-16'
        content = raw[2:]
    else:
        # Pas de BOM, essayer UTF-8
        encoding = 'utf-8'
        content = raw
    
    return content.decode(encoding)
```

### Tests de robustesse

**Cas de test essentiels :**
- Fichiers avec/sans BOM
- Tous les endianness
- Corruption partielle
- Mélange d'encodages

**Exemple de test :**
```python
def test_encodings():
    test_cases = [
        ('utf-8', b'Hello \xc3\xa9'),           # é
        ('utf-16', b'\xff\xfeH\x00e\x00'),      # He
        ('utf-8-sig', b'\xef\xbb\xbfHello'),    # BOM + Hello
    ]
    
    for encoding, bytes_data in test_cases:
        try:
            text = bytes_data.decode(encoding)
            print(f"{encoding}: {text!r}")
        except UnicodeDecodeError as e:
            print(f"{encoding}: ERROR - {e}")
```

### Migration legacy

**Stratégie :**
1. **Audit :** Identifier tous les fichiers problématiques
2. **Conversion :** Normaliser vers UTF-8 sans BOM
3. **Tests :** Vérifier que rien ne casse
4. **Monitoring :** Surveiller les nouveaux fichiers

**Outil de conversion massive :**
```bash
# Linux: convertir tout un répertoire
find . -name "*.txt" -exec bash -c '
  encoding=$(file -b --mime-encoding "$1")
  if [ "$encoding" != "utf-8" ]; then
    iconv -f "$encoding" -t utf-8 "$1" > "${1}.tmp" && mv "${1}.tmp" "$1"
  fi
' _ {} \;
```

Maîtriser BOM et endianness est crucial pour éviter les corruptions de données silencieuses qui peuvent ruiner la fiabilité d'une application internationalisée.