# Chapitre 12 — Fonts et rendu

## Introduction

Les polices (fonts) sont l'interface entre les code points Unicode abstraits et leur rendu visuel concret. Comprendre cette couche est crucial pour maîtriser l'affichage correct du texte international.

## Glyphes vs caractères

### Distinction conceptuelle

**Caractère Unicode :** Concept abstrait (signification)
**Glyphe :** Forme visuelle concrète (apparence)

**Exemple :** Le caractère "A" (U+0041) peut être rendu comme :
- **A** (Times New Roman)
- **A** (Arial)
- **𝐀** (mathématique)
- **𝒜** (calligraphique)

### Substitution de glyphes

Une police peut définir **plusieurs glyphes** pour le même caractère :
- **Contextuels :** Ligatures en fonction du contexte
- **Stylistiques :** Variantes de formes
- **Positionnels :** Différentes formes selon la position

## TrueType, OpenType

### Formats de polices modernes

**TrueType (TTF) :**
- **Créé par Apple** (1980s)
- **Mathématiques :** Courbes quadratiques
- **Avantages :** Qualité, compatibilité
- **Limites :** Fonctionnalités avancées limitées

**OpenType (OTF) :**
- **Évolution :** Microsoft + Adobe (2000)
- **Mathématiques :** Courbes cubiques (meilleure qualité)
- **Tables avancées :** GSUB, GPOS (voir chapitre 13)
- **Support Unicode :** Complet

### Différences techniques

**TrueType :**
```text
glyf table : Quadratic curves
cmap table : Character to glyph mapping
```

**OpenType :**
```text
glyf/CFF  : Cubic/quadratic curves
GSUB      : Glyph substitution rules
GPOS      : Glyph positioning
```

## Ligatures

### Définition et types

**Ligature :** Fusion visuelle de plusieurs caractères

**Types :**
- **Standard :** fi, fl, æ, œ
- **Contextuelles :** Historiques, stylistiques
- **Discretionnelles :** Artistiqes (Th, ct)

### Exemples concrets

**Latin :**
- **fi** → ﬁ (ligature standard)
- **Th** → Th (ligature discrétionnaire)

**Arabe :** Ligatures obligatoires pour la calligraphie
**Devanagari :** Ligatures complexes pour les combinaisons

### Contrôle utilisateur

**CSS :**
```css
/* Forcer les ligatures */
font-variant-ligatures: common-ligatures;

/* Désactiver */
font-variant-ligatures: none;

/* Ligatures discrétionnaires */
font-variant-ligatures: discretionary-ligatures;
```

## Fallback de polices

### Mécanisme de fallback

Quand une police **ne contient pas** un glyphe :

1. **Substitution :** Glyphe générique (□)
2. **Fallback chain :** Liste de polices alternatives
3. **System fallback :** Police système par défaut

### Configuration CSS

```css
font-family: 
  "Ma Police Principale",
  "Segoe UI",           /* Windows */
  "Helvetica Neue",     /* macOS */
  Ubuntu,               /* Linux */
  sans-serif;           /* Generic */
```

### Problèmes courants

**Mélange de polices :**
- Largeurs incohérentes
- Styles différents
- Métriques incompatibles

**Solutions :**
- **Web fonts :** @font-face
- **System font stacks :** Polices cohérentes
- **Variable fonts :** Ajustement dynamique

## Pourquoi □ apparaît

### Caractère de remplacement

**U+FFFD (�) :** Indique un problème d'encodage

**Causes courantes :**
- **Police manquante :** Glyphe non disponible
- **Encodage corrompu :** Bytes invalides
- **Police inappropriée :** Support Unicode insuffisant

### Diagnostic

**Étapes de debug :**
1. **Vérifier l'encodage :** Est-ce du UTF-8 valide ?
2. **Tester la police :** Contient-elle le glyphe ?
3. **Vérifier le rendu :** Problème de fallback ?

**Outils :**
```javascript
// Vérifier si glyphe disponible
function hasGlyph(fontFamily, char) {
  const canvas = document.createElement('canvas');
  const ctx = canvas.getContext('2d');
  ctx.font = `12px ${fontFamily}`;
  const width = ctx.measureText(char).width;
  return width > 0; // Glyphe existe si largeur > 0
}
```

### Prévention

**Best practices :**
- **Test cross-platform :** Différents OS et navigateurs
- **Web fonts :** Fournir polices complètes
- **Graceful degradation :** Fallback appropriés
- **Validation :** Vérifier le support avant utilisation

La maîtrise des polices et du rendu est essentielle pour une expérience utilisateur internationale cohérente.