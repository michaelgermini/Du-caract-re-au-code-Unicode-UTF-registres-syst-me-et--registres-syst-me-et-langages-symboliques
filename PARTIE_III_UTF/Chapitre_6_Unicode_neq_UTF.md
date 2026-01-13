# Chapitre 6 — Unicode ≠ UTF

## Introduction

Une confusion extrêmement répandue est d'assimiler Unicode à UTF-8. Cette erreur conceptuelle empêche de comprendre correctement le fonctionnement du texte moderne.

Ce chapitre démystifie la relation entre Unicode (l'abstraction) et UTF (l'encodage), distinction cruciale pour tout développeur travaillant avec du texte international.

## Confusion courante

### Le mythe Unicode = UTF-8

**Erreur commune :**
```text
"Unicode" = "UTF-8"
"Mon fichier est en Unicode" = "Mon fichier est en UTF-8"
```

**Réalité :**
- Unicode est une **norme abstraite** définissant des caractères
- UTF-8 est un **encodage concret** pour stocker ces caractères
- Unicode peut être encodé en UTF-8, UTF-16, UTF-32, etc.

### Conséquences pratiques

**Problème fréquent :**
Un développeur dit "mon application supporte Unicode" mais ne gère que UTF-8, causant des bugs avec :
- Fichiers UTF-16 (Windows)
- Texte UTF-32 (bases de données)
- Encodages legacy convertis partiellement

## Unicode comme abstraction

### Unicode : norme conceptuelle

Unicode définit :
- **Code points :** Identifiants abstraits (U+0041 pour 'A')
- **Propriétés :** Comportement des caractères (casse, direction, etc.)
- **Relations :** Correspondances entre caractères similaires

**Exemple conceptuel :**
```
Caractère : A
Code point : U+0041
Propriétés :
  - Nom : LATIN CAPITAL LETTER A
  - Casse : Majuscule
  - Correspondance minuscule : U+0061
  - Script : Latin
  - Direction : Left-to-Right
```

### Séparation abstraction/implémentation

**Unicode (abstrait) :**
- Définit ce qu'est un "A"
- Spécifie son comportement
- Indépendant du stockage

**UTF (concret) :**
- Spécifie comment stocker le "A"
- Définit la représentation binaire
- Dépend de l'usage (mémoire, disque, réseau)

## UTF comme encodage binaire

### UTF : Unicode Transformation Format

UTF désigne une **famille d'encodages** pour représenter les code points Unicode en octets :

**Formats disponibles :**
- **UTF-8 :** Variable (1-4 octets), compatible ASCII
- **UTF-16 :** Variable (2-4 octets), optimisé pour BMP
- **UTF-32 :** Fixe (4 octets), simplicité maximale

### Comparaison des encodages

**Même texte "A" :**

| Format | Code point | Représentation | Avantages | Inconvénients |
|--------|------------|----------------|-----------|--------------|
| UTF-8  | U+0041    | 0x41          | Compact, ASCII-compatible | Variable |
| UTF-16 | U+0041    | 0x0041        | Bon compromis | Surrogates complexes |
| UTF-32 | U+0041    | 0x00000041    | Simple | Gaspilleur |

## Pourquoi c'est crucial en informatique

### Choix d'encodage : compromis techniques

**Critères de choix :**

1. **Espace mémoire :**
   - UTF-8 : Économique pour texte latin
   - UTF-32 : Simple mais gourmand
   - UTF-16 : Bon milieu

2. **Performance :**
   - UTF-8 : Parsing complexe (variable)
   - UTF-32 : Accès direct par index
   - UTF-16 : Mixte

3. **Compatibilité :**
   - UTF-8 : Hérité ASCII/C
   - UTF-16 : Windows, Java
   - UTF-32 : Bases de données, APIs

### Exemples d'usage réel

**Navigateurs web :**
- **Stockage interne :** UTF-16 (JavaScript)
- **Transmission :** UTF-8 (HTTP)
- **Affichage :** Selon la police

**Systèmes d'exploitation :**
- **Linux :** UTF-8 partout
- **Windows :** UTF-16 en interne, conversions
- **macOS :** UTF-8 pour fichiers, UTF-16 pour APIs

**Bases de données :**
- **MySQL :** Supporte UTF-8, UTF-16, UTF-32
- **PostgreSQL :** UTF-8 principalement
- **Oracle :** UTF-16 historique

### Problèmes de conversion

**Scénario typique :**
1. Application lit fichier UTF-16
2. Traite comme UTF-8
3. Corruption des caractères multi-octets

**Code buggé :**
```c
// ERREUR : suppose UTF-8
char* text = read_file_utf16("data.txt");
process_utf8_text(text); // Corruption !
```

**Correct :**
```c
char16_t* text_utf16 = read_file_utf16("data.txt");
char* text_utf8 = convert_utf16_to_utf8(text_utf16);
process_utf8_text(text_utf8);
```

## Implications pour l'architecture logicielle

### Design pattern : séparation encodage/contenu

**Architecture moderne :**
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Application   │    │   Bibliothèque   │    │     Stockage    │
│   (Unicode)     │◄──►│   (Conversion)  │◄──►│   (UTF-8/16)   │
│                 │    │                 │    │                 │
│ - Code points   │    │ - utf8_to_utf16 │    │ - Fichiers      │
│ - Propriétés    │    │ - utf16_to_utf32│    │ - Réseau       │
│ - Normalisation │    │ - validation    │    │ - APIs         │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Bonnes pratiques

1. **Stocker en UTF-8 :** Standard web, compact pour texte latin
2. **Traiter en Unicode :** Utiliser des bibliothèques ICU, etc.
3. **Convertir à la frontière :** Séparer logique métier et encodage
4. **Valider les entrées :** Détecter les encodages incorrects

### Outils recommandés

**Bibliothèques :**
- **ICU (International Components for Unicode) :** Référence
- **iconv :** Conversions système
- **Python :** builtin UTF support
- **JavaScript :** TextEncoder/TextDecoder

**Outils de debug :**
- **hexdump :** Voir les octets bruts
- **file :** Détecter l'encodage
- **Unicode code charts :** Comprendre les code points

Comprendre cette distinction Unicode/UTF est fondamental pour éviter les bugs d'internationalisation et concevoir des systèmes textuels robustes. Le chapitre suivant détaille UTF-8, l'encodage le plus populaire.