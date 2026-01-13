# Chapitre 4 — L'espace Unicode

## Introduction

L'espace Unicode est organisé de manière hiérarchique et logique pour accommoder la diversité immense des systèmes d'écriture humains. Comprendre cette organisation est crucial pour travailler efficacement avec Unicode.

Ce chapitre explore la structure de l'espace Unicode, des plans aux zones spécialisées.

## Les plans Unicode

### Organisation hiérarchique

Unicode divise son espace en **17 plans** (planes) de 65,536 code points chacun :

**Structure générale :**
- **Plan 0 :** Basic Multilingual Plane (BMP) - U+0000 à U+FFFF
- **Plans 1-16 :** Supplementary Planes - U+10000 à U+10FFFF
- **Total :** 1,114,112 code points possibles

### Pourquoi cette organisation ?

1. **Compatibilité historique :** Le BMP contient la plupart des caractères utilisés quotidiennement
2. **Extension progressive :** Nouveaux plans ajoutés selon les besoins
3. **Performance :** BMP souvent traité différemment (UTF-16, etc.)

## BMP (Basic Multilingual Plane)

### Définition et importance

Le BMP contient les caractères les plus fréquemment utilisés et assure la compatibilité maximale.

**Plage :** U+0000 à U+FFFF (65,536 code points)
**Contenu principal :**
- Langues européennes modernes
- Arabe, hébreu
- Asie de l'Est (CJK)
- Symboles courants

### Organisation interne du BMP

**Zones principales :**
- **U+0000-U+007F :** ASCII (compatibilité)
- **U+0080-U+00FF :** Latin-1 Supplement
- **U+0100-U+017F :** Latin Extended-A
- **U+0180-U+024F :** Latin Extended-B
- **U+0250-U+02AF :** IPA Extensions
- **U+02B0-U+02FF :** Spacing Modifier Letters
- **U+0300-U+036F :** Combining Diacritical Marks
- **U+0370-U+03FF :** Greek and Coptic
- **U+0400-U+04FF :** Cyrillic
- **U+0500-U+052F :** Cyrillic Supplement
- **U+0530-U+058F :** Armenian
- **U+0590-U+05FF :** Hebrew
- **U+0600-U+06FF :** Arabic
- **U+0700-U+074F :** Syriac
- **U+0750-U+077F :** Arabic Supplement
- **U+0780-U+07BF :** Thaana
- **U+07C0-U+07FF :** NKo
- **U+0800-U+083F :** Samaritan
- **U+0840-U+085F :** Mandaic
- **U+0860-U+086F :** Syriac Supplement
- **U+08A0-U+08FF :** Arabic Extended-A
- **U+0900-U+097F :** Devanagari
- **U+0980-U+09FF :** Bengali
- **U+0A00-U+0A7F :** Gurmukhi
- **U+0A80-U+0AFF :** Gujarati
- **U+0B00-U+0B7F :** Oriya
- **U+0B80-U+0BFF :** Tamil
- **U+0C00-U+0C7F :** Telugu
- **U+0C80-U+0CFF :** Kannada
- **U+0D00-U+0D7F :** Malayalam
- **U+0D80-U+0DFF :** Sinhala
- **U+0E00-U+0E7F :** Thai
- **U+0E80-U+0EFF :** Lao
- **U+0F00-U+0FFF :** Tibetan
- **U+1000-U+109F :** Myanmar
- **U+10A0-U+10FF :** Georgian
- **U+1100-U+11FF :** Hangul Jamo
- **U+1200-U+137F :** Ethiopic
- **U+1380-U+139F :** Ethiopic Supplement
- **U+13A0-U+13FF :** Cherokee
- **U+1400-U+167F :** Unified Canadian Aboriginal Syllabics
- **U+1680-U+169F :** Ogham
- **U+16A0-U+16FF :** Runic
- **U+1700-U+171F :** Tagalog
- **U+1720-U+173F :** Hanunoo
- **U+1740-U+175F :** Buhid
- **U+1760-U+177F :** Tagbanwa
- **U+1780-U+17FF :** Khmer
- **U+1800-U+18AF :** Mongolian
- **U+1900-U+194F :** Limbu
- **U+1950-U+197F :** Tai Le
- **U+1980-U+19DF :** New Tai Lue
- **U+19E0-U+19FF :** Khmer Symbols
- **U+1A00-U+1A1F :** Buginese
- **U+1A20-U+1A5F :** Tai Tham
- **U+1A60-U+1A7F :** Combining Tai Lue
- **U+1A80-U+1A9F :** Tai Viet
- **U+1AA0-U+1AAF :** Cham
- **U+1AB0-U+1AFF :** Combining Diacritical Marks Extended
- **U+1B00-U+1B7F :** Balinese
- **U+1B80-U+1BBF :** Sundanese
- **U+1BC0-U+1BFF :** Batak
- **U+1C00-U+1C4F :** Lepcha
- **U+1C50-U+1C7F :** Ol Chiki
- **U+1C80-U+1C8F :** Cyrillic Extended-C
- **U+1C90-U+1CBF :** Georgian Extended
- **U+1CC0-U+1CCF :** Sundanese Supplement
- **U+1CD0-U+1CFF :** Vedic Extensions
- **U+1D00-U+1D7F :** Phonetic Extensions
- **U+1D80-U+1DBF :** Phonetic Extensions Supplement
- **U+1DC0-U+1DFF :** Combining Diacritical Marks Supplement
- **U+1E00-U+1EFF :** Latin Extended Additional
- **U+1F00-U+1FFF :** Greek Extended
- **U+2000-U+206F :** General Punctuation
- **U+2070-U+209F :** Superscripts and Subscripts
- **U+20A0-U+20CF :** Currency Symbols
- **U+20D0-U+20FF :** Combining Diacritical Marks for Symbols
- **U+2100-U+214F :** Letterlike Symbols
- **U+2150-U+218F :** Number Forms
- **U+2190-U+21FF :** Arrows
- **U+2200-U+22FF :** Mathematical Operators
- **U+2300-U+23FF :** Miscellaneous Technical
- **U+2400-U+243F :** Control Pictures
- **U+2440-U+245F :** Optical Character Recognition
- **U+2460-U+24FF :** Enclosed Alphanumerics
- **U+2500-U+257F :** Box Drawing
- **U+2580-U+259F :** Block Elements
- **U+25A0-U+25FF :** Geometric Shapes
- **U+2600-U+26FF :** Miscellaneous Symbols
- **U+2700-U+27BF :** Dingbats
- **U+27C0-U+27EF :** Miscellaneous Mathematical Symbols-A
- **U+27F0-U+27FF :** Supplemental Arrows-A
- **U+2800-U+28FF :** Braille Patterns
- **U+2900-U+297F :** Supplemental Arrows-B
- **U+2980-U+29FF :** Miscellaneous Mathematical Symbols-B
- **U+2A00-U+2AFF :** Supplemental Mathematical Operators
- **U+2B00-U+2BFF :** Miscellaneous Symbols and Arrows
- **U+2C00-U+2C5F :** Glagolitic
- **U+2C60-U+2C7F :** Latin Extended-C
- **U+2C80-U+2CFF :** Coptic
- **U+2D00-U+2D2F :** Georgian Supplement
- **U+2D30-U+2D7F :** Tifinagh
- **U+2D80-U+2DDF :** Ethiopic Extended
- **U+2DE0-U+2DFF :** Cyrillic Extended-A
- **U+2E00-U+2E7F :** Supplemental Punctuation
- **U+2E80-U+2EFF :** CJK Radicals Supplement
- **U+2F00-U+2FDF :** Kangxi Radicals
- **U+2FF0-U+2FFF :** Ideographic Description Characters
- **U+3000-U+303F :** CJK Symbols and Punctuation
- **U+3040-U+309F :** Hiragana
- **U+30A0-U+30FF :** Katakana
- **U+3100-U+312F :** Bopomofo
- **U+3130-U+318F :** Hangul Compatibility Jamo
- **U+3190-U+319F :** Kanbun
- **U+31A0-U+31BF :** Bopomofo Extended
- **U+31C0-U+31EF :** CJK Strokes
- **U+31F0-U+31FF :** Katakana Phonetic Extensions
- **U+3200-U+32FF :** Enclosed CJK Letters and Months
- **U+3300-U+33FF :** CJK Compatibility
- **U+3400-U+4DBF :** CJK Unified Ideographs Extension A
- **U+4DC0-U+4DFF :** Yijing Hexagram Symbols
- **U+4E00-U+9FFF :** CJK Unified Ideographs
- **U+A000-U+A48F :** Yi Syllables
- **U+A490-U+A4CF :** Yi Radicals
- **U+A4D0-U+A4FF :** Lisu
- **U+A500-U+A63F :** Vai
- **U+A640-U+A69F :** Cyrillic Extended-B
- **U+A6A0-U+A6FF :** Bamum
- **U+A700-U+A71F :** Modifier Tone Letters
- **U+A720-U+A7FF :** Latin Extended-D
- **U+A800-U+A82F :** Syloti Nagri
- **U+A830-U+A83F :** Common Indic Number Forms
- **U+A840-U+A87F :** Phags-pa
- **U+A880-U+A8DF :** Saurashtra
- **U+A8E0-U+A8FF :** Devanagari Extended
- **U+A900-U+A92F :** Kayah Li
- **U+A930-U+A95F :** Rejang
- **U+A960-U+A97F :** Hangul Jamo Extended-A
- **U+A980-U+A9DF :** Javanese
- **U+A9E0-U+A9FF :** Myanmar Extended-B
- **U+AA00-U+AA5F :** Cham
- **U+AA60-U+AA7F :** Myanmar Extended-A
- **U+AA80-U+AADF :** Tai Viet
- **U+AAE0-U+AAFF :** Meetei Mayek Extensions
- **U+AB00-U+AB2F :** Ethiopic Extended-A
- **U+AB30-U+AB6F :** Latin Extended-E
- **U+AB70-U+ABBF :** Cherokee Supplement
- **U+ABC0-U+ABFF :** Meetei Mayek
- **U+AC00-U+D7AF :** Hangul Syllables
- **U+D7B0-U+D7FF :** Hangul Jamo Extended-B
- **U+D800-U+DB7F :** High Surrogates (UTF-16)
- **U+DB80-U+DBFF :** High Private Use Surrogates
- **U+DC00-U+DFFF :** Low Surrogates (UTF-16)
- **U+E000-U+F8FF :** Private Use Area
- **U+F900-U+FAFF :** CJK Compatibility Ideographs
- **U+FB00-U+FB4F :** Alphabetic Presentation Forms
- **U+FB50-U+FDFF :** Arabic Presentation Forms-A
- **U+FE00-U+FE0F :** Variation Selectors
- **U+FE10-U+FE1F :** Vertical Forms
- **U+FE20-U+FE2F :** Combining Half Marks
- **U+FE30-U+FE4F :** CJK Compatibility Forms
- **U+FE50-U+FE6F :** Small Form Variants
- **U+FE70-U+FEFF :** Arabic Presentation Forms-B
- **U+FF00-U+FFEF :** Halfwidth and Fullwidth Forms
- **U+FFF0-U+FFFF :** Specials

## Plans supplémentaires

### Plan 1 : Supplementary Multilingual Plane (SMP)

**Plage :** U+10000 à U+1FFFF
**Contenu :**
- Écritures anciennes (Linéaire B, hiéroglyphes égyptiens)
- Symboles musicaux
- Mathématiques avancées
- Émoji historiques

**Exemples :**
- U+10140-U+1018F : Greek Acrophonic Numerals
- U+10300-U+1032F : Old Italic
- U+10330-U+1034F : Gothic
- U+10380-U+1039F : Ugaritic

### Plan 2 : Supplementary Ideographic Plane (SIP)

**Plage :** U+20000 à U+2FFFF
**Contenu :** Idéogrammes CJK rares et historiques

### Plans 3-13 : Planes non assignés

Réservés pour une expansion future.

### Plan 14 : Supplementary Special-purpose Plane (SSP)

**Plage :** U+0E0000 à U+0EFFFF
**Contenu :** Usage spécial (tags, etc.)

### Plans 15-16 : Private Use Planes

**Plage :** U+F0000 à U+10FFFF
**Usage :** Applications privées, polices personnalisées

## Emoji, symboles, écritures anciennes

### Émoji dans Unicode

Les émoji occupent plusieurs zones :
- **BMP :** Émoji simples (U+1F600-U+1F64F)
- **SMP :** Émoji complexes et séquences

**Organisation :**
- Visages : U+1F600-U+1F64F
- Gestes : U+1F645-U+1F64F
- Objets : U+1F4A0-U+1F5FF
- Symboles : U+1F300-U+1F5FF

### Écritures anciennes

Unicode préserve les systèmes d'écriture historiques :
- **Sumérien :** U+12000-U+123FF
- **Égyptien hiéroglyphique :** U+13000-U+1342F
- **Cuneiform :** U+12000-U+123FF

### Symboles spécialisés

- **Mathématiques :** U+2200-U+22FF, U+1D400-U+1D7FF
- **Monnaies :** U+20A0-U+20CF
- **Flèches :** U+2190-U+21FF, U+2900-U+297F

## Limite U+10FFFF

### Origines de la limite

La limite U+10FFFF vient des contraintes techniques d'UTF-16 :
- **UTF-16 :** Utilise des surrogates pour les code points > U+FFFF
- **Surrogates :** U+D800-U+DFFF (2048 valeurs)
- **Calcul :** U+FFFF + (2048 × 1024) = U+10FFFF

### Conséquences

1. **Espace fini :** 1,114,112 code points maximum
2. **Planification :** Attribution soigneuse des code points
3. **Extension future :** Nouvelle version d'Unicode si nécessaire

### Utilisation actuelle

**Statistiques (Unicode 15.1) :**
- Code points assignés : ~149,000
- Espace disponible : ~965,000 code points
- Taux d'utilisation : ~13%

Cette organisation hiérarchique permet à Unicode de s'adapter à l'évolution des besoins linguistiques tout en maintenant la compatibilité et la performance.