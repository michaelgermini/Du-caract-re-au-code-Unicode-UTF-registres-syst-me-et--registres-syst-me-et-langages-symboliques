# Annexe D — Glossaire

## Termes fondamentaux

### A

**ASCII (American Standard Code for Information Interchange)**
: Système d'encodage 7 bits (128 caractères) créé en 1963. Base de tous les encodages modernes.

**Abstract Character**
: Concept Unicode d'un caractère comme entité sémantique abstraite, indépendante de sa forme visuelle.

### B

**Basic Multilingual Plane (BMP)**
: Premier plan Unicode (U+0000 à U+FFFF) contenant la plupart des caractères utilisés quotidiennement.

**Byte Order Mark (BOM)**
: Caractère spécial (U+FEFF) placé au début des fichiers pour indiquer l'encodage et l'endianness.

### C

**Code Point**
: Valeur numérique unique assignée à chaque caractère Unicode, notée U+XXXX.

**Combining Character**
: Caractère qui modifie le précédent (accents, diacritiques) sans prendre d'espace horizontal.

**Cyrillique**
: Système d'écriture utilisé pour le russe, bulgare, serbe, etc. (U+0400-U+04FF).

### D

**Diacritique**
: Marque ajoutée à une lettre pour modifier sa prononciation (accent, cédille, tréma).

**Devanagari**
: Système d'écriture pour l'hindi, le sanskrit, etc. Caractères composés complexes.

### E

**EBCDIC (Extended Binary Coded Decimal Interchange Code)**
: Ancien encodage IBM 8 bits, incompatible avec ASCII.

**Emoji**
: Pictogrammes modernes stockés en Unicode, souvent multi-code points.

**Endianness**
: Ordre de stockage des octets en mémoire (big-endian vs little-endian).

### F

**Fallback (de police)**
: Mécanisme remplaçant une police manquante par une alternative appropriée.

### G

**Grapheme Cluster**
: Unité visuelle perçue comme "un caractère" (peut contenir plusieurs code points).

**Glyph**
: Forme visuelle concrète d'un caractère, dépendante de la police.

### H

**Hangul**
: Système d'écriture coréen (U+1100-U+11FF pour Jamo, U+AC00-U+D7AF pour syllabes).

**Hexadécimal**
: Système numérique base 16 (0-9, A-F) utilisé pour les code points Unicode.

### I

**ICU (International Components for Unicode)**
: Bibliothèque de référence pour le traitement Unicode (normalisation, collation, etc.).

**ISO 8859**
: Famille d'encodages 8 bits pour différentes régions linguistiques.

### J

**Jamo**
: Lettres individuelles du Hangul coréen utilisées pour composer les syllabes.

**JIS X 0208**
: Standard japonais définissant 7,000 caractères (Kanji, Kana).

### K

**Kanji**
: Caractères chinois utilisés en japonais, souvent avec plusieurs lectures.

### L

**Latin**
: Alphabet utilisé pour l'anglais, français, espagnol, etc. (U+0000-U+024F).

**Ligature**
: Fusion visuelle de plusieurs lettres (fi, æ, œ).

### M

**Mark (diacritical)**
: Caractère de combinaison modifiant l'apparence d'une lettre de base.

### N

**NFC (Normalization Form Canonical Composition)**
: Forme normalisée composant les caractères compatibles.

**NFD (Normalization Form Canonical Decomposition)**
: Forme normalisée décomposant les caractères en base + marques.

**NFKC/NFKD (Compatibility)**
: Formes normalisées gérant les variantes stylistiques.

### O

**OpenType**
: Format moderne de polices supportant Unicode et fonctionnalités avancées.

### P

**Plane (Unicode)**
: Division d'Unicode en blocs de 65,536 code points (17 plans au total).

**Private Use Area (PUA)**
: Zones réservées pour usage privé (U+E000-U+F8FF, planes 15-16).

### S

**Script**
: Système d'écriture complet (Latin, Cyrillique, Arabe, etc.).

**Surrogate**
: Paire de code points (high+low) encodant un caractère au-delà du BMP en UTF-16.

### T

**TrueType**
: Ancien format de polices remplacé par OpenType.

### U

**UCS (Universal Character Set)**
: Nom technique d'Unicode (ISO 10646).

**UTF-8**
: Encodage Unicode variable (1-4 octets), compatible ASCII.

**UTF-16**
: Encodage Unicode variable (2-4 octets), optimisé pour BMP.

**UTF-32**
: Encodage Unicode fixe (4 octets), simplicité maximale.

**Unicode**
: Standard universel de codage des caractères (depuis 1991).

### V

**Variation Selector**
: Caractères (U+FE00-U+FE0F) sélectionnant des variantes stylistiques d'un caractère de base.

### Z

**Zero Width Joiner (ZWJ, U+200D)**
: Caractère invisible forçant la composition visuelle d'émoji.

**Zero Width Non-Joiner (ZWNJ, U+200C)**
: Caractère invisible empêchant les ligatures indésirables.

Ce glossaire couvre les termes essentiels pour maîtriser Unicode et les encodages de caractères.