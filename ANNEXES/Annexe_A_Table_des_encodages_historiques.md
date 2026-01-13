# Annexe A — Table des encodages historiques

## Vue d'ensemble chronologique

Cette annexe présente une **chronologie complète** des encodages de caractères depuis les origines jusqu'à Unicode.

## Avant 1960 : Systèmes propriétaires

### Téléscripteurs (1930s-1950s)

| Système | Année | Caractères | Usage |
|---------|-------|------------|-------|
| Baudot | 1874 | 5 bits | Télégraphe |
| ITA-1 | 1924 | 5 bits | Téléimprimeurs |
| ITA-2 | 1930 | 5 bits | ASCII précurseur |

**Limites :** Majuscules seulement, pas de chiffres/symboles

### Mainframes IBM (1950s-1960s)

| Encodage | Bits | Caractères | Particularités |
|----------|------|------------|----------------|
| EBCDIC | 8 bits | 256 | IBM mainframes |
| BCD | 6 bits | 64 | Calculatrices |

**EBCDIC spécial :**
- A = 193 (vs ASCII 65)
- Différent ordre des lettres
- Symboles à des positions étranges

## 1960s : Naissance de l'ASCII

### ASCII 1963

**Spécifications :**
- **Bits :** 7 bits (128 caractères)
- **Contrôle :** 0x00-0x1F (33 caractères)
- **Imprimable :** 0x20-0x7E (95 caractères)
- **Parité :** 8ème bit souvent parité

**Table complète :**

| Hex | Décimal | Caractère | Description |
|-----|---------|-----------|-------------|
| 00 | 0 | NUL | Null |
| 01 | 1 | SOH | Start of Heading |
| ... | ... | ... | ... |
| 20 | 32 | SP | Space |
| 21 | 33 | ! | Exclamation mark |
| ... | ... | ... | ... |
| 41 | 65 | A | Lettre A |
| ... | ... | ... | ... |
| 7E | 126 | ~ | Tilde |
| 7F | 127 | DEL | Delete |

### Extensions ASCII (1970s)

**ASCII étendu :**
- **8 bits :** 256 caractères
- **Haut :** 128 caractères supplémentaires
- **Problème :** Conflits régionaux

## 1980s : Encodages nationaux

### Europe occidentale

| Pays | Encodage | Année | Particularités |
|------|----------|-------|----------------|
| France | ISO-646-FR | 1970s | é, à, è, ù, ç |
| Allemagne | ISO-646-DE | 1970s | ä, ö, ü, ß |
| Espagne | ISO-646-ES | 1970s | ñ, ¿, ¡ |

### Asie de l'Est

| Région | Encodage | Bits | Caractères |
|--------|----------|------|------------|
| Japon | JIS X 0201 | 7 bits | Katakana + ASCII |
| Japon | JIS X 0208 | - | 7,000 Kanji |
| Chine | GB 2312 | - | 6,000 caractères |
| Corée | KS X 1001 | - | 8,000 Hangul |

**JIS X 0208 :**
- **Structure :** 94×94 matrice
- **Kana :** Hiragana, Katakana
- **Kanji :** Jōyō kanji (2,136)

## 1990s : Vers l'internationalisation

### ISO 8859 (1980s-1990s)

**Famille complète :**

| Partie | Région | Année | Caractères clés |
|--------|--------|-------|-----------------|
| 8859-1 | Europe occidentale | 1987 | é, ç, ñ |
| 8859-2 | Europe centrale | 1987 | ł, ř, š |
| 8859-3 | Europe sud | 1988 | ĝ, ĵ, ĥ |
| 8859-4 | Europe nord | 1988 | ā, ķ, ņ |
| 8859-5 | Cyrillique | 1988 | а, б, в |
| 8859-6 | Arabe | 1987 | alif, ba, ta |
| 8859-7 | Grec | 1987 | α, β, γ |
| 8859-8 | Hébreu | 1988 | א, ב, ג |
| 8859-9 | Turc | 1989 | ğ, ş, ı |
| 8859-10 | Nordique | 1992 | ð, þ, ŋ |
| 8859-11 | Thaï | 2001 | ก, ข, ค |
| 8859-13 | Baltique | 1998 | ā, č, ē |
| 8859-14 | Celtique | 1998 | ḃ, ċ, ḋ |
| 8859-15 | Europe occidentale v2 | 1999 | €, œ, Ÿ |
| 8859-16 | Europe sud-est | 2001 | ă, ș, ț |

### Windows code pages (1990s)

**CP 125x series :**

| Code page | Région | Base | Extensions |
|-----------|--------|------|------------|
| 1250 | Europe centrale | 8859-2 | €, Œ, ™ |
| 1251 | Cyrillique | 8859-5 | €, Ё, Ђ |
| 1252 | Europe occidentale | 8859-1 | €, œ, Ÿ |
| 1253 | Grec | 8859-7 | €, ₯ |
| 1254 | Turc | 8859-9 | €, Ğ, Ş |
| 1255 | Hébreu | 8859-8 | €, ₪ |
| 1256 | Arabe | 8859-6 | €, ₤ |
| 1257 | Baltique | 8859-13 | €, Ŗ, ė |
| 1258 | Vietnamien | 8859-1 | Ắ, Ằ, Ẳ |

### Asie : Encodages complexes

**Japon :**
- **Shift-JIS :** Variable, populaire sur PC
- **EUC-JP :** Unix, plus propre
- **ISO-2022-JP :** Email, 7 bits

**Chine :**
- **GBK :** Extension de GB2312
- **GB18030 :** Unicode-compatible

**Corée :**
- **EUC-KR :** Extension de KS X 1001
- **CP949 :** Windows Korea

## Problèmes structurels

### Conflits de code pages

**Même octet, caractères différents :**

| Valeur | ASCII | CP437 | ISO-8859-1 | CP1252 |
|--------|-------|-------|------------|--------|
| 0x80 | (contrôle) | Ç | (contrôle) | € |
| 0x85 | (contrôle) | å | (contrôle) | … |
| 0x99 | (contrôle) | ö | (contrôle) | ™ |

### Impossibilité de multilinguisme

**Document multilingue :**
- Français + allemand = impossible
- Même langue + symboles = problèmes

### Gestion logicielle complexe

**Applications 1990s :**
- Tables de conversion multiples
- Détection automatique d'encodage
- Support limité des langues asiatiques

## Transition vers Unicode (1990s-2000s)

### Unicode 1.0 (1991)

**Première version :**
- 7,161 caractères
- BMP uniquement
- ISO 10646 compatible

### Adoption progressive

**1990s :** Support partiel dans applications
**2000s :** Adoption généralisée
**2010s :** Standard de facto

Cette chronologie montre comment le chaos des encodages a naturellement conduit à Unicode comme solution universelle.