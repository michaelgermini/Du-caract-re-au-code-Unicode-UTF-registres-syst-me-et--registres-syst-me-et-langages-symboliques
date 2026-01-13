# Chapitre 5 — Zones spéciales

## Introduction

Certaines zones d'Unicode ne contiennent pas des "caractères" au sens traditionnel, mais des éléments spéciaux qui modifient le comportement du texte, assurent la compatibilité, ou permettent des usages avancés.

Comprendre ces zones spéciales est crucial pour maîtriser Unicode et éviter les pièges courants.

## Combining characters

### Principe fondamental

Les combining characters (caractères de combinaison) ne s'affichent pas seuls mais modifient le caractère qui les précède.

**Mécanisme :**
1. **Caractère de base :** Lettre ou symbole principal
2. **Combining character :** Modificateur (accent, diacritique)
3. **Résultat :** Glyphe composé visuellement

### Exemples concrets

**Accent aigu :**
```
Base : e (U+0065)
Combining : ́ (U+0301)
Résultat : é
```

**Comparaison avec caractères précomposés :**
```
Précomposé : é (U+00E9)
Décomposé : e + ́ (U+0065 + U+0301)
Rendu identique, mais représentations différentes
```

### Zones de combining characters

**Combining Diacritical Marks (U+0300-U+036F) :**
- **U+0300-U+031F :** Diacritiques de base (´, `, ¨, ˆ)
- **U+0320-U+033F :** Diacritiques inférieurs (˘, ˚, ˛)
- **U+0340-U+035F :** Diacritiques supplémentaires

**Combining Half Marks (U+FE20-U+FE2F) :**
- Marques de ligature pour l'hébreu et l'arabe

### Implications pour les développeurs

**Problèmes courants :**
```javascript
// Même texte, représentations différentes
const text1 = "é";     // U+00E9
const text2 = "é";     // U+0065 U+0301

console.log(text1 === text2); // false !
console.log(text1.length);    // 1
console.log(text2.length);    // 2 (en unités UTF-16)
```

**Solution :** Utiliser la normalisation Unicode (voir chapitre 10)

## Control characters

### Définition et rôle

Les control characters contrôlent le comportement du texte plutôt que d'afficher du contenu visible.

**Catégories :**
- **C0 Controls :** U+0000-U+001F (ASCII controls)
- **C1 Controls :** U+0080-U+009F (ISO controls)
- **Format Controls :** U+200C-U+200F, U+202A-U+202E

### Principaux control characters

**C0 Controls (U+0000-U+001F) :**
- **U+0000 :** NULL (fin de chaîne C)
- **U+0009 :** TAB (\t)
- **U+000A :** LF (Line Feed, \n)
- **U+000D :** CR (Carriage Return, \r)
- **U+001B :** ESC (Escape)

**Format Controls :**
- **U+200C :** ZWNJ (Zero Width Non-Joiner)
- **U+200D :** ZWJ (Zero Width Joiner)
- **U+200E :** LRM (Left-to-Right Mark)
- **U+200F :** RLM (Right-to-Left Mark)

### ZWNJ et ZWJ : contrôle des ligatures

**ZWNJ (U+200C) :** Empêche les ligatures
```
Persan : نمی‌خواهم (avec ZWNJ)
Sans ZWNJ : نمیخواهم (ligaturé)
```

**ZWJ (U+200D) :** Force les ligatures
```
Emoji : 👨‍👩‍👧‍👦 (famille composée)
Sans ZWJ : 👨 👩 👧 👦 (séparés)
```

## Surrogates

### Origines techniques

Les surrogates résolvent une limitation d'UTF-16 : représenter des code points > U+FFFF.

**Principe :**
- **High Surrogate :** U+D800-U+DBFF (1024 valeurs)
- **Low Surrogate :** U+DC00-U+DFFF (1024 valeurs)
- **Combinaison :** 1024 × 1024 = 1,048,576 code points supplémentaires

### Calcul du code point

Pour une paire surrogate H+L :
```
Code point = (H - 0xD800) × 0x400 + (L - 0xDC00) + 0x10000
```

**Exemple :**
- **U+1F600 (😀)**
- High : U+D83D = (0xD83D - 0xD800) × 0x400 = 0x5 × 0x400 = 0x1400
- Low : U+DE00 = (0xDE00 - 0xDC00) = 0x200
- Résultat : 0x1400 + 0x200 + 0x10000 = 0x11600 = U+1F600 ✓

### Dangers des surrogates isolés

**Problèmes :**
- High surrogate seul : invalide
- Low surrogate seul : invalide
- Séquence incorrecte : corruption

**Exemple de corruption :**
```text
Texte original : 😀 (U+1F600 = D83D DE00)
Octets corrompus : D83D DE01
Résultat : 😁 (U+1F601, léger changement)
```

## Private Use Area (PUA)

### Définition et philosophie

Le Private Use Area permet aux organisations et individus de définir leurs propres caractères sans coordination avec le Consortium Unicode.

**Zones PUA :**
- **BMP PUA :** U+E000-U+F8FF (6,400 code points)
- **Plane 15 :** U+F0000-U+FFFFF (65,536 code points)
- **Plane 16 :** U+100000-U+10FFFF (65,536 code points)

### Cas d'usage légitimes

**Polices personnalisées :**
- Symboles d'entreprise
- Notation musicale spécialisée
- Émoji internes

**Exemple concret :**
Une entreprise de chimie pourrait définir :
- U+E000 : Symbole spécial pour un composé
- U+E001 : Notation pour une réaction

### Problèmes et controverses

**Risques :**
- **Incompatibilité :** PUA d'une police ≠ PUA d'une autre
- **Portabilité :** Texte PUA inutilisable sans la police appropriée
- **Conflits :** Même code point, significations différentes

**Controverse :**
- Apple, Google, Microsoft ont utilisé PUA pour les émoji avant Unicode 6.0
- Cela a créé des conflits entre plateformes

## Cas d'usage réel des PUA

### Polices techniques spécialisées

**Exemple : Symboles musicaux étendus**
```
U+F400 : ♪ (standard Unicode)
U+E000 : ♫ (variante stylisée)
U+E001 : ♬ (notation spéciale)
```

### Logiciels internes

**Systèmes de trading :**
- U+F0000-U+F0FFF : Symboles boursiers personnalisés
- U+F1000-U+F1FFF : Indicateurs techniques

### Jeux vidéo

**MMORPG :**
- U+E000-U+EFFF : Objets de jeu personnalisés
- U+F000-U+FFFF : Émoji de guilde

### Bonnes pratiques PUA

1. **Documentation :** Documenter clairement les assignations PUA
2. **Évitement des conflits :** Utiliser des plages dédiées
3. **Conversion :** Prévoir la migration vers Unicode standard quand possible
4. **Fallback :** Gérer l'absence de police appropriée

### Alternative moderne : Variation Selectors

Pour les variantes stylistiques, utiliser les Variation Selectors (U+FE00-U+FE0F) plutôt que PUA :

```
Base : 😀 (U+1F600)
VS-1 : 😀️ (U+1F600 U+FE0F) - émoji style
VS-16 : 😀︎ (U+1F600 U+FE0E) - texte style
```

Comprendre ces zones spéciales permet d'éviter de nombreux pièges et de tirer parti des fonctionnalités avancées d'Unicode. Le chapitre suivant explore comment ces concepts abstraits sont encodés en octets binaires avec UTF-8.