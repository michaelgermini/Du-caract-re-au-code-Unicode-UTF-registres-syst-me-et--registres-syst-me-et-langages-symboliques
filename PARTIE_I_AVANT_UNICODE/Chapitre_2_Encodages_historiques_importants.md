# Chapitre 1 — Quand le texte n'était pas universel

## Introduction

Avant l'avènement d'Unicode en 1991, le monde informatique vivait dans un chaos textuel où chaque pays, chaque système d'exploitation, et parfois même chaque application utilisait son propre système d'encodage des caractères. Cette fragmentation a causé des problèmes majeurs de compatibilité qui persistent encore aujourd'hui dans certaines applications legacy.

Dans ce chapitre, nous explorerons les origines de ce chaos et ses conséquences pratiques pour comprendre pourquoi Unicode était nécessaire.

## L'ASCII : naissance et limites

### Origines de l'ASCII

L'ASCII (American Standard Code for Information Interchange) fut créé en 1963 par l'ANSI (American National Standards Institute). À l'époque, l'informatique était dominée par les États-Unis, et l'ASCII reflétait cette réalité culturelle.

**Caractéristiques techniques :**
- **Taille :** 7 bits (128 caractères possibles)
- **Plage :** 0x00 à 0x7F (0 à 127 en décimal)
- **Structure :**
  - 0x00-0x1F : Caractères de contrôle (null, tab, line feed, etc.)
  - 0x20-0x2F : Symboles de ponctuation et opérateurs
  - 0x30-0x39 : Chiffres (0-9)
  - 0x40-0x5F : Symboles et opérateurs supplémentaires
  - 0x60-0x6F : Lettres minuscules
  - 0x70-0x7F : Lettres majuscules et symboles

**Exemple concret :**
```ascii
A = 65 (0x41)
a = 97 (0x61)
0 = 48 (0x30)
```

### Limites fondamentales de l'ASCII

L'ASCII souffrait de limitations majeures qui rendaient impossible la représentation universelle du texte :

1. **Taille insuffisante :** 128 caractères ne suffisent pas pour représenter toutes les langues du monde
2. **Centrage américain :** Aucun accent européen, pas de symboles non-latins
3. **Pas de distinction majuscules/minuscules cohérente** (bien que les deux soient présents)

**Exemple de limitation :**
- Le français nécessite des caractères comme é, è, à, ù, ç
- L'espagnol utilise ñ, ¿, ¡
- L'allemand a des caractères comme ß, ä, ö, ü

## Les encodages nationaux

Face aux limitations de l'ASCII, chaque pays développa ses propres extensions, créant un patchwork d'encodages incompatibles.

### Le principe des extensions ASCII

La plupart des systèmes étendirent l'ASCII à 8 bits (256 caractères) en utilisant l'octet supplémentaire pour les caractères locaux :

- **Bits 0-6 :** ASCII standard (inchangé)
- **Bit 7 :** Extension locale (128 caractères supplémentaires)

### Exemples d'encodages nationaux

**Français (ISO-8859-1 / Latin-1) :**
- Position 0xE0 : à
- Position 0xE9 : é
- Position 0xE8 : è
- Position 0xF9 : ù

**Allemand :**
- Position 0xDF : ß
- Position 0xE4 : ä
- Position 0xF6 : ö
- Position 0xFC : ü

**Arabe, Chinois, Japonais :** Systèmes complètement différents utilisant des méthodes de codage variables.

## Code pages (DOS, Windows, ISO)

### Code pages DOS

Sous MS-DOS, Microsoft utilisa le système des "code pages" (CP) :

- **CP 437 :** Code page originale DOS (caractères semi-graphiques)
- **CP 850 :** Europe de l'Ouest (Multilingual)
- **CP 852 :** Europe Centrale
- **CP 866 :** Cyrillique russe

**Exemple CP 850 :**
```
┌─┬─┐  Box drawing characters
│ │ │
└─┴─┘
```

### Code pages Windows

Windows continua cette tradition avec des code pages plus sophistiquées :

- **CP 1252 :** Windows-1252 (Latin-1 amélioré)
- **CP 1251 :** Cyrillique
- **CP 1253 :** Grec
- **CP 1254 :** Turc

### Standards ISO

L'ISO (International Organization for Standardization) créa la famille ISO-8859 :

- **ISO-8859-1 :** Latin-1 (Europe occidentale)
- **ISO-8859-2 :** Latin-2 (Europe centrale)
- **ISO-8859-5 :** Cyrillique
- **ISO-8859-6 :** Arabe
- **ISO-8859-7 :** Grec

## Problèmes de compatibilité

### Incompatibilité entre systèmes

Le même octet représentait des caractères différents selon l'encodage :

**Exemple concret :**
- En CP 1252 : 0x80 = € (Euro)
- En CP 1250 : 0x80 = Ŕ (R avec accent)
- En ISO-8859-1 : 0x80 = (caractère de contrôle)

### Problèmes d'échange de fichiers

**Scénario typique :**
1. Document créé en France (ISO-8859-1)
2. Ouvert en Allemagne (ISO-8859-2)
3. Tous les accents deviennent des caractères cyrilliques ou grecs
4. Le texte devient illisible

### Applications multilingues

Les applications devant supporter plusieurs langues devaient :
- Détecter l'encodage automatiquement
- Convertir entre encodages
- Gérer des polices multiples
- Maintenir des tables de conversion complexes

## Perte de sens et corruption de données

### Corruption lors de transferts

**Exemple réel :**
```text
Texte original (Français, ISO-8859-1) : "école"
Bytes : E9 63 6F 6C 65

Lu en CP 1252 : "écolé" (légèrement différent)
Lu en ISO-8859-2 : "école" (complètement différent)
```

### Problèmes en bases de données

Les bases de données stockaient souvent du texte sans métadonnées d'encodage, causant :
- Corruption lors de migrations
- Recherches impossibles sur du texte multilingue
- Tri incorrect des données

### Impact économique

Cette fragmentation coûtait cher aux entreprises :
- Développement de logiciels de conversion
- Support technique pour problèmes d'encodage
- Perte de données critiques
- Incompatibilité internationale

## Leçon apprise

Le chaos pré-Unicode nous enseigne une leçon cruciale : **l'encodage des caractères doit être universel, explicite, et indépendant de la plateforme**. Cette prise de conscience mena directement à la création d'Unicode.

Dans le chapitre suivant, nous explorerons les encodages historiques importants qui tentèrent de résoudre ces problèmes avant Unicode.