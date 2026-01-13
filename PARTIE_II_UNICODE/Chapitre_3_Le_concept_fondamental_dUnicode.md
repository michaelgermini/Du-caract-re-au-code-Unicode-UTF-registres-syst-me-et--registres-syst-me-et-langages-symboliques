# Chapitre 3 — Le concept fondamental d'Unicode

## Introduction

Unicode représente une révolution conceptuelle dans l'informatique : pour la première fois, un système d'encodage visait à représenter **tous les caractères utilisés par l'humanité**, passés et présents. Cette ambition universelle nécessitait une refonte complète des concepts de base.

Ce chapitre explore les principes fondamentaux d'Unicode qui le distinguent de tous les systèmes précédents.

## Code point : définition

### Qu'est-ce qu'un code point ?

Un code point Unicode est l'unité atomique de texte dans Unicode. Chaque caractère, symbole, ou élément textuel se voit assigner un numéro unique appelé "code point".

**Notation :** U+ suivi d'un nombre hexadécimal
**Exemples :**
- U+0041 : Lettre A latine
- U+03B1 : Lettre α grecque
- U+3042 : Hiragana あ japonais

### Caractéristiques techniques

**Plage complète :** U+0000 à U+10FFFF (1,114,112 code points possibles)
**Structure :**
- **Plane 0 (BMP) :** U+0000 à U+FFFF (65,536 code points)
- **Planes 1-16 :** U+10000 à U+10FFFF (supplémentaires)

### Différence cruciale avec les encodages précédents

**Avant Unicode :**
```text
'A' en ASCII     = 65
'A' en EBCDIC    = 193
'A' en ISO-8859-1 = 65
```

**Avec Unicode :**
```text
'A' latine = U+0041 (toujours, partout)
'Α' grec   = U+0391 (différent, unique)
'А' cyrillique = U+0410 (différent, unique)
```

## Séparation caractère / glyphe

### Caractère vs Glyphe : distinction fondamentale

Cette séparation conceptuelle est l'innovation la plus importante d'Unicode.

**Définition :**
- **Caractère :** Unité abstraite de texte (signification sémantique)
- **Glyphe :** Représentation visuelle concrète (forme d'affichage)

### Exemple concret

Le caractère "A" (U+0041) peut être rendu de multiples façons :
- **A** : Times New Roman
- **A** : Arial
- **𝐀** : Mathématique grasse
- **𝒜** : Calligraphique

Tous représentent le même caractère abstrait, mais avec des glyphes différents.

### Implications techniques

1. **Mêmes données, rendus différents :** Le texte Unicode peut être affiché différemment selon la police
2. **Même rendu, données différentes :** Des glyphes identiques peuvent provenir de code points différents
3. **Flexibilité typographique :** Support natif pour les variantes stylistiques

## Caractère abstrait vs rendu visuel

### Le caractère comme concept abstrait

Un caractère Unicode n'est pas une image ou une forme, mais une **idée abstraite** avec des propriétés définies :
- **Nom canonique :** "LATIN CAPITAL LETTER A"
- **Catégorie générale :** Lettre majuscule
- **Propriétés bidirectionnelles :** Direction gauche-à-droite
- **Propriétés de casse :** Minuscule correspondante = U+0061

### Propriétés Unicode des caractères

Chaque code point possède des propriétés standardisées :

**Exemple pour U+0041 (A) :**
```properties
Name: LATIN CAPITAL LETTER A
General_Category: Lu (Letter, uppercase)
Script: Latn (Latin)
Alphabetic: Yes
Uppercase: Yes
Lowercase_Mapping: U+0061 (a)
```

### Rendu visuel : rôle de la police

Le rendu visuel dépend entièrement de la police (font) :
- **Police manquante :** □ (caractère de remplacement)
- **Police inappropriée :** Glyphe incorrect ou absent
- **Police complète :** Glyphe correct selon le style

## Universalité et neutralité culturelle

### Ambition universelle

Unicode ne se limite pas aux langues vivantes :
- **Langues anciennes :** Linéaire B, hiéroglyphes égyptiens
- **Symboles mathématiques :** ∀ ∃ ∈ ⊆ ⊇
- **Émoji modernes :** 😀 🎉 🚀
- **Symboles techniques :** ⚡ 🔧 🛠️

### Neutralité culturelle

Contrairement aux encodages précédents centrés sur l'anglais/américain :
- **Pas de langue privilégiée**
- **Support égal pour toutes les écritures**
- **Décisions techniques, pas politiques**

### Exemple de diversité

**Écritures supportées :**
- **Latines :** Anglais, français, vietnamien, haoussa
- **Cyrilliques :** Russe, serbe, mongol
- **Arabes :** Arabe, persan, ourdou
- **Asiatiques :** Chinois, japonais, coréen
- **Indiennes :** Hindi, tamoul, thaï
- **Africaines :** Amharique, éthiopien
- **Autres :** Hébreu, arménien, géorgien

## Le Unicode Consortium

### Histoire et organisation

Fondé en 1991 par des entreprises technologiques (Apple, Microsoft, Xerox, etc.), le Consortium Unicode gère l'évolution de la norme.

**Mission :**
- Assigner des code points de manière ordonnée
- Définir les propriétés des caractères
- Coordonner avec les organismes de standardisation (ISO/IEC 10646)

### Processus d'ajout de caractères

**Étapes :**
1. **Proposition :** Expert ou organisation propose un caractère
2. **Révision :** Comité technique examine la proposition
3. **Approbation :** Consensus requis
4. **Publication :** Nouvelle version d'Unicode

### Versions d'Unicode

**Historique :**
- **Unicode 1.0 (1991) :** 7,161 caractères
- **Unicode 2.0 (1996) :** Émoji, symboles
- **Unicode 15.1 (2023) :** Plus de 149,000 caractères

### Gouvernance moderne

- **Siège :** Mountain View, Californie
- **Membres :** Plus de 400 organisations
- **Processus ouvert :** Participation publique possible

## Implications pour les développeurs

### Nouvelle façon de penser le texte

**Avant Unicode :**
```c
// Texte = tableau d'octets
char text[] = "Hello";
int length = 5; // Toujours vrai
```

**Avec Unicode :**
```c
// Texte = séquence de code points
// Encodage = représentation binaire
const char32_t text[] = U"Hello"; // U+0048, U+0065, U+006C, U+006C, U+006F
```

### Complexité accrue

Unicode introduit des complexités inconnues auparavant :
- **Encodages multiples :** UTF-8, UTF-16, UTF-32
- **Normalisation :** Même texte, représentations différentes
- **Grapheme clusters :** "Un caractère" n'existe plus vraiment
- **Propriétés complexes :** Bidirectionnel, combinaison, etc.

Cette complexité est le prix de l'universalité. Le chapitre suivant explore l'espace Unicode et comment ces code points sont organisés.