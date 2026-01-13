# Chapitre 10 — Normalisation Unicode

## Introduction

La normalisation Unicode est l'un des concepts les plus importants et mal compris de l'informatique moderne. Elle résout le problème fondamental où "le même" texte peut avoir des représentations binaires différentes, causant des bugs subtils dans la recherche, le tri et la comparaison.

Ce chapitre explore les mécanismes de normalisation et leurs implications pratiques.

## NFC, NFD, NFKC, NFKD

### Les quatre formes de normalisation

Unicode définit **quatre formes normalisées** standard :

**NFC (Canonical Composition) :**
- **Composition :** Combine les caractères compatibles
- **Usage :** Forme la plus courante, économise l'espace

**NFD (Canonical Decomposition) :**
- **Décomposition :** Sépare les caractères composés
- **Usage :** Analyse linguistique, recherche

**NFKC (Compatibility Composition) :**
- **Composition + Compatibilité :** Normalise les variantes stylistiques
- **Usage :** Sécurité, normalisation agressive

**NFKD (Compatibility Decomposition) :**
- **Décomposition + Compatibilité :** Forme la plus décomposée
- **Usage :** Recherche insensible aux variantes

### Exemples concrets

**Caractère :** é (e accent aigu)

**Représentations possibles :**
- **Précomposé :** é (U+00E9)
- **Décomposé :** e + ́ (U+0065 + U+0301)

**Normalisation :**
```text
Texte original : "café"
NFC  : café (U+0063 U+0061 U+0066 U+00E9)
NFD  : café (U+0063 U+0061 U+0066 U+0065 U+0301)
NFKC : café (même que NFC pour cet exemple)
NFKD : café (même que NFD pour cet exemple)
```

### Différences subtiles

**Exemple avec ligatures :**
```text
Texte : "fi" (ligature)
NFC/NFD  : fi (ligature préservée)
NFKC/NFKD : fi (décomposé en f + i)
```

**Exemple avec fractions :**
```text
Texte : "½" (fraction)
NFC/NFD  : ½ (préservé)
NFKC/NFKD : 1/2 (décomposé)
```

## Accents combinés

### Mécanisme des combining characters

Les accents en Unicode utilisent deux approches :

**Caractères précomposés :**
- **é :** U+00E9 (un seul code point)
- **Avantage :** Simple, compact
- **Limite :** Un caractère par combinaison

**Combining characters :**
- **é :** U+0065 + U+0301 (base + accent)
- **Avantage :** Flexible, extensible
- **Complexité :** Rendu plus complexe

### Gestion des accents multiples

**Exemple :** Ố (o avec horn et accent aigu)

**Précomposé :** Ố (U+1ED0)
**Décomposé :** o + ̛ + ́ (U+006F + U+031B + U+0301)

### Ordre des accents

L'ordre des combining characters est **significatif** :

```text
Base : a (U+0061)
Accent grave : à (U+0061 + U+0300)
Accent aigu : á (U+0061 + U+0301)
Mais : à́ (deux accents) = á̀ (ordre différent)
```

## Comparaison de chaînes

### Problème fondamental

**Sans normalisation :**
```javascript
const text1 = "café";     // U+0063 U+0061 U+0066 U+00E9
const text2 = "café";     // U+0063 U+0061 U+0066 U+0065 U+0301

console.log(text1 === text2); // false !
text1.localeCompare(text2);   // ≠ 0
```

**Avec normalisation :**
```javascript
const nfc1 = text1.normalize('NFC');
const nfc2 = text2.normalize('NFC');

console.log(nfc1 === nfc2); // true ✓
```

### Algorithmes de comparaison

**Comparaison binaire :** Échoue sur représentations différentes
**Comparaison normalisée :** Fonctionne mais coûteuse
**Collations :** Comparaison linguistique (voir chapitre 14)

## Sécurité et homoglyphes

### Attaques par homoglyphes

Les homoglyphes sont des caractères **visuellement identiques** mais différents en Unicode :

**Exemples dangereux :**
- **a (latin) :** U+0061
- **а (cyrillique) :** U+0430
- **Visuellement :** a vs a (identiques !)

**Domaines malveillants :**
- **paypal.com vs pаypal.com** (a cyrillique)
- **apple.com vs аррӏе.com** (a, p, l cyrilliques)

### NFKC vs NFC pour la sécurité

**NFC :** Préserve les différences stylistiques
**NFKC :** Normalise les variantes stylistiques

**Exemple :**
```text
Domaine suspect : "аррӏе.com" (cyrillique)
NFC  : аррӏе.com (reste différent)
NFKC : apple.com (normalisé !)
```

### Implémentation de sécurité

**Fonction de validation :**
```python
import unicodedata

def normalize_domain(domain):
    # NFKC pour sécurité
    normalized = unicodedata.normalize('NFKC', domain)
    # Convertir en ASCII si possible
    try:
        ascii_domain = normalized.encode('ascii').decode('ascii')
        return ascii_domain.lower()
    except UnicodeEncodeError:
        # Contient des caractères non-ASCII
        return normalized.lower()

# Test
print(normalize_domain("аррӏе.com"))  # "apple.com"
print(normalize_domain("paypal.com")) # "paypal.com"
```

## Cas concrets en bases de données

### Problème des recherches

**Base de données avec données mixtes :**
```sql
-- Données stockées sans normalisation
INSERT INTO users (name) VALUES ('café');    -- précomposé
INSERT INTO users (name) VALUES ('café');    -- décomposé

-- Recherche échoue
SELECT * FROM users WHERE name = 'café';     -- trouve seulement 1 résultat
```

### Solutions

**1. Normaliser à l'entrée :**
```sql
-- PostgreSQL
CREATE OR REPLACE FUNCTION normalize_text(text)
RETURNS text AS $$
BEGIN
  RETURN normalize($1, NFC);
END;
$$ LANGUAGE plpgsql;

-- Contrainte
ALTER TABLE users ADD CONSTRAINT name_normalized
  CHECK (name = normalize_text(name));
```

**2. Normaliser dans les requêtes :**
```sql
-- Recherche normalisée
SELECT * FROM users
WHERE normalize_text(name) = normalize_text('café');
```

**3. Index fonctionnel :**
```sql
-- MySQL
CREATE INDEX idx_name_normalized ON users
  ((CONVERT(name USING utf8mb4) COLLATE utf8mb4_unicode_ci));

-- Recherche insensible à la normalisation
SELECT * FROM users WHERE name = 'café' COLLATE utf8mb4_unicode_ci;
```

### Migration de données existantes

**Stratégie de migration :**
```sql
-- 1. Créer colonne normalisée
ALTER TABLE users ADD COLUMN name_normalized TEXT;

-- 2. Remplir avec données normalisées
UPDATE users SET name_normalized = normalize_text(name);

-- 3. Créer index
CREATE INDEX idx_name_normalized ON users (name_normalized);

-- 4. Mettre à jour l'application
-- 5. Remplacer la colonne originale (optionnel)
```

### Performances

**Comparaison :**

| Méthode | Avantages | Inconvénients |
|---------|-----------|--------------|
| Normalisation stockage | Requêtes simples | Migration coûteuse |
| Normalisation requêtes | Stockage inchangé | Requêtes lentes |
| Index fonctionnel | Performance | Complexité |

### Cas d'usage avancés

**Recherche partielle :**
```sql
-- Recherche dans texte normalisé
SELECT * FROM articles
WHERE normalize_text(content) LIKE normalize_text('%café%');
```

**Tri correct :**
```sql
-- Tri linguistique après normalisation
SELECT name FROM users
ORDER BY normalize_text(name) COLLATE "fr_FR";
```

La normalisation Unicode est essentielle pour la fiabilité des applications internationales, particulièrement en bases de données et dans les systèmes de recherche.