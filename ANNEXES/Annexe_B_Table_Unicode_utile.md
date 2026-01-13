# Annexe B — Table Unicode utile

## Code points essentiels

### Contrôles et formats

| Code point | Nom | Usage |
|------------|-----|-------|
| U+0000 | NULL | Fin de chaîne C |
| U+0009 | TAB | Tabulation |
| U+000A | LF | Line Feed (\n) |
| U+000D | CR | Carriage Return (\r) |
| U+0020 | SPACE | Espace standard |
| U+00A0 | NO-BREAK SPACE | Espace insécable |
| U+200B | ZERO WIDTH SPACE | Espace de largeur nulle |
| U+200C | ZWNJ | Zero Width Non-Joiner |
| U+200D | ZWJ | Zero Width Joiner |
| U+200E | LRM | Left-to-Right Mark |
| U+200F | RLM | Right-to-Left Mark |
| U+2028 | LINE SEPARATOR | Séparateur de ligne |
| U+2029 | PARAGRAPH SEPARATOR | Séparateur de paragraphe |
| U+FEFF | BOM | Byte Order Mark |

### Symboles courants

| Catégorie | Exemples |
|-----------|----------|
| **Monnaie** | $ € ¥ £ ¢ ₹ ₽ |
| **Mathématiques** | + − × ÷ = ≈ ≠ |
| **Flèches** | ← → ↑ ↓ ↔ ⇒ |
| **Ponctuation** | " ' « » ‚ „ |
| **Fractions** | ½ ⅓ ¼ ¾ |

### Émoji populaires

| Catégorie | Émoji | Code points |
|-----------|-------|-------------|
| **Visages** | 😀 😢 😊 | U+1F600, U+1F622, U+1F60A |
| **Cœurs** | ❤️ 💙 💜 | U+2764 U+FE0F, etc. |
| **Mains** | 👍 👎 👌 | U+1F44D, U+1F44E, U+1F44C |
| **Objets** | 💻 📱 ⌚ | U+1F4BB, U+1F4F1, U+231A |

## Zones spéciales

### Private Use Area

| Zone | Plage | Usage |
|------|-------|-------|
| BMP PUA | U+E000-U+F8FF | Applications privées |
| Plane 15 | U+F0000-U+FFFFF | Organisations |
| Plane 16 | U+100000-U+10FFFF | Usage privé étendu |

### Surrogates (UTF-16)

| Zone | Plage | Usage |
|------|-------|-------|
| High Surrogates | U+D800-U+DBFF | Premier octet UTF-16 |
| Low Surrogates | U+DC00-U+DFFF | Second octet UTF-16 |

## Propriétés Unicode importantes

### Catégories générales

| Code | Signification | Exemples |
|------|----------------|----------|
| Lu | Letter uppercase | A, B, C |
| Ll | Letter lowercase | a, b, c |
| Lt | Letter titlecase | ǅ, ǈ |
| Lm | Letter modifier | ʰ, ʳ |
| Lo | Letter other | א, ب, 中 |
| Mn | Mark nonspacing | ́ ̀ ̂ |
| Mc | Mark spacing | ि, ี |
| Me | Mark enclosing | ⃝ |
| Nd | Number decimal | 0, 1, 2 |
| Nl | Number letter | Ⅳ, Ⅴ |
| No | Number other | ½, ² |
| Pc | Punctuation connector | _ |
| Pd | Punctuation dash | - |
| Ps | Punctuation open | ( [ { |
| Pe | Punctuation close | ) ] } |
| Pi | Punctuation initial | " ' |
| Pf | Punctuation final | " ' |
| Po | Punctuation other | ! , . |
| Sm | Symbol math | + = × |
| Sc | Symbol currency | $ € ¥ |
| Sk | Symbol modifier | ^ ` |
| So | Symbol other | © ® ™ |
| Zs | Separator space | [espace] |
| Zl | Separator line | U+2028 |
| Zp | Separator paragraph | U+2029 |
| Cc | Other control | [tab] [lf] |
| Cf | Other format | U+200E |
| Cs | Other surrogate | U+D800 |
| Co | Other private use | U+E000 |
| Cn | Other not assigned | (réservé) |

## Tables de conversion rapide

### ASCII (0x00-0x7F)

```
0x20: [espace]
0x30-0x39: 0-9
0x41-0x5A: A-Z
0x61-0x7A: a-z
```

### Latin-1 (0x80-0xFF)

```
0xA0-0xBF: Ponctuation, symboles
0xC0-0xCF: Lettres accentuées majuscules
0xD0-0xDF: Lettres accentuées minuscules
0xE0-0xEF: Autres lettres accentuées
0xF0-0xFF: Symboles, fractions
```

### Émoji blocks

- **U+1F600-U+1F64F :** Émoji visages
- **U+1F300-U+1F5FF :** Symboles et objets
- **U+1F680-U+1F6FF :** Transport et cartes
- **U+1F900-U+1F9FF :** Supplément émoji
- **U+2600-U+26FF :** Symboles divers
- **U+2700-U+27BF :** Dingbats

Cette annexe sert de référence rapide pour les code points les plus utilisés en développement.