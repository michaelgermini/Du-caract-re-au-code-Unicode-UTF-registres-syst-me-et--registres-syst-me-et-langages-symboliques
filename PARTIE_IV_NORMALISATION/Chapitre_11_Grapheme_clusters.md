# Chapitre 11 — Grapheme clusters

## Introduction

La notion de "caractère" en Unicode est beaucoup plus complexe que l'intuition le suggère. Un grapheme cluster représente ce que l'utilisateur perçoit comme "un caractère" à l'écran, mais qui peut être composé de multiples code points Unicode.

Comprendre les grapheme clusters est crucial pour l'interface utilisateur et l'expérience utilisateur modernes.

## Ce qu'est "un caractère" visuellement

### Distinction fondamentale

**Code point :** Unité abstraite d'Unicode (U+0041)
**Grapheme cluster :** Unité visuelle perçue par l'utilisateur

**Exemples :**
- **A** : 1 code point = 1 grapheme cluster
- **é** : 1 code point (précomposé) = 1 grapheme cluster
- **é** : 2 code points (décomposé) = 1 grapheme cluster
- **👨‍👩‍👧‍👦** : 7 code points = 1 grapheme cluster

### Algorithme de détermination

Unicode définit des **règles complexes** pour grouper les code points en grapheme clusters :

1. **Grapheme base :** Lettre, chiffre, symbole, émoji
2. **Combining characters :** Accents, diacritiques
3. **Grapheme extenders :** Marques supplémentaires
4. **ZWJ sequences :** Ligatures et compositions

## Emoji composés

### ZWJ (Zero Width Joiner)

Le caractère U+200D **relie visuellement** des émoji :

**Exemples :**
- **👨‍👩‍👧‍👦** : Famille (homme + femme + fille + garçon)
- **👨‍💻** : Développeur homme
- **👩‍🚀** : Astronaute femme
- **🏳️‍🌈** : Drapeau arc-en-ciel

### Composition complexe

**Structure d'un émoji composé :**
```
Base emoji + ZWJ + Modificateur + ZWJ +Autre
👨 + U+200D + 💻 = 👨‍💻
```

### Gestion en programmation

**JavaScript :**
```javascript
const family = "👨‍👩‍👧‍👦";
console.log(family.length);     // 11 (code units UTF-16)
console.log([...family].length); // 7 (code points)
console.log(family);            // 1 (grapheme clusters)
```

**Swift :**
```swift
let family = "👨‍👩‍👧‍👦"
print(family.count)              // 1 (grapheme clusters)
print(family.unicodeScalars.count) // 7 (code points)
```

## ZWJ (Zero Width Joiner)

### Propriétés techniques

**U+200D :**
- **Largeur :** 0 (invisible)
- **Catégorie :** Format control
- **Rôle :** Forcer la composition visuelle

### Cas d'usage avancés

**Ligatures complexes :**
- **Flag sequences :** 🇺🇸 = 🇺 + 🇸 (pas de ZWJ nécessaire)
- **Keycaps :** 1️⃣ = 1 + U+FE0F + U+20E3
- **Couleurs :** 🏳️‍🌈 = 🏳️ + U+200D + 🌈

### Différences régionales

Certains émoji ZWJ ne s'affichent pas sur tous les systèmes :
- **👨‍👩‍👧‍👦** : Supporté partout
- **👨‍🚀** : Peut ne pas composer sur anciens systèmes

## Pourquoi len() ment parfois

### Problèmes courants

**Python 2 (obsolète) :**
```python
text = "👨‍👩‍👧‍👦"
print(len(text))  # 11 (code units UTF-16 mal décodés)
```

**Python 3 :**
```python
text = "👨‍👩‍👧‍👦"
print(len(text))  # 7 (code units UTF-16)
# Mais utilisateur voit 1 caractère !
```

**Solution :**
```python
import regex as re

def count_graphemes(text):
    # Utilise la regex Unicode pour grapheme clusters
    return len(re.findall(r'\X', text))

print(count_graphemes("👨‍👩‍👧‍👦"))  # 1 ✓
print(count_graphemes("café"))        # 4 ✓
```

### Langages modernes

**JavaScript ES2021+ :**
```javascript
const text = "👨‍👩‍👧‍👦";
console.log(text.length);        // 7 (code units)
console.log([...text].length);   // 7 (code points)
// Pas de comptage grapheme natif
```

**Swift :**
```swift
let text = "👨‍👩‍👧‍👦"
print(text.count)  // 1 (grapheme clusters) ✓
```

## Impacts en UI et UX

### Saisie de texte

**Problèmes de curseur :**
- **Cliquer au milieu d'un émoji composé**
- **Sélection partielle**
- **Suppression incorrecte**

**Solutions UI :**
- **Grapheme-aware navigation**
- **Sélection par clusters**
- **Affichage spécial des ZWJ**

### Stockage et transmission

**Base de données :**
```sql
-- Stocker en UTF-8
-- Mais compter et rechercher par graphemes
SELECT LENGTH(CONVERT(name USING utf8mb4)) as bytes,
       CHAR_LENGTH(name) as codepoints
FROM users;
```

**APIs :**
```json
{
  "text": "👨‍👩‍👧‍👦",
  "length_bytes": 25,
  "length_codepoints": 7,
  "length_graphemes": 1
}
```

### Recherche et indexation

**Problèmes :**
- **Recherche :** "famille" ne trouve pas "👨‍👩‍👧‍👦"
- **Tri :** Ordre visuel ≠ ordre Unicode
- **Indexation :** Comptage incorrect

**Solutions :**
- **Index grapheme-aware**
- **Recherche par mots-clés alternatifs**
- **Normalisation pour recherche**

### Accessibilité

**Lecteurs d'écran :**
- Doivent annoncer "famille" pas "homme, femme, fille, garçon"
- Gestion spéciale des ZWJ sequences

**Navigation clavier :**
- **Flèche gauche/droite :** Par grapheme cluster
- **Delete/Backspace :** Par grapheme cluster

### Performance

**Coûts computationnels :**
- **len(text)** : O(1)
- **count_graphemes(text)** : O(n)
- **Impact UI :** Calcul fréquent nécessaire

**Optimisations :**
- **Cache des comptages**
- **Lazy evaluation**
- **Approximations** (suffisant pour UI)

Comprendre les grapheme clusters est essentiel pour créer des interfaces utilisateur intuitives dans un monde Unicode complexe.