# Chapitre 17 — Unicode et sécurité

## Introduction

Unicode introduit des dimensions de sécurité complètement nouvelles et souvent sous-estimées. La richesse des caractères et des règles de normalisation crée des opportunités d'attaques sophistiquées qui exploitent la complexité du texte international.

Ce chapitre explore les vulnérabilités Unicode et les stratégies de protection.

## Homoglyph attacks

### Principe fondamental

Les **homoglyphes** sont des caractères visuellement identiques mais différents en Unicode, permettant de créer des domaines ou noms d'utilisateur malveillants.

**Exemple classique :**
```
Domaine légitime : paypal.com
Domaine malveillant : раypal.com (p cyrillique)
```

Visuellement identiques, mais :
- **p latin** : U+0070
- **р cyrillique** : U+0440

### Types d'homoglyphes

**1. Lettres similaires :**
```
a (latin) U+0061 vs а (cyrillique) U+0430
o (latin) U+006F vs о (cyrillique) U+043E
p (latin) U+0070 vs р (cyrillique) U+0440
```

**2. Chiffres similaires :**
```
0 (zéro) U+0030 vs О (cyrillique) U+041E
1 (un) U+0031 vs l (L minuscule) U+006C
```

**3. Symboles confondus :**
```
/ (slash) U+002F vs ⁄ (fraction slash) U+2044
- (tiret) U+002D vs ― (tiret long) U+2015
```

### Attaques réelles

**Phishing domains :**
- **аррӏе.com** (cyrillique + l spécial)
- **mіcrosoft.com** (i cyrillique)
- **wіkіpedia.org** (i cyrillique)

**Attaques sur noms d'utilisateur :**
```javascript
// Système vulnérable
const user1 = "admin";
const user2 = "аdmin";  // a cyrillique

console.log(user1 === user2);  // false (différents)
console.log(user1 == user2);   // false
// Mais visuellement identiques !
```

### Scripts confondus

**Mélange de scripts :**
```
Latin + Cyrillique : аdmin (russe + latin)
Latin + Grec : αdmin
Latin + Cheroke : Ꭰdmin
```

**Détection difficile :**
- Même apparence visuelle
- Différents code points Unicode
- Validation par script complexe

## Domaines IDN

### Internationalized Domain Names

**IDN** permet des domaines en caractères non-ASCII :
- **münchen.de** (allemand)
- **пример.испытание** (russe)
- **例え.テスト** (japonais)

**Processus :**
1. **Encodage Punycode** : Conversion Unicode → ASCII
2. **Préfixe xn--** : Indicateur d'IDN
3. **Résolution DNS** : Fonctionnement transparent

**Exemple :**
```
Domaine : münchen.de
Punycode : xn--mnchen-3ya.de
```

### Vulnérabilités IDN

**Attaques homographe :**
```
paypal.com
xn--pypal-4ve.com (pypal avec y grec)
```

**Problèmes :**
- **Navigateurs** : Affichent le Unicode, cachent le Punycode
- **Spoofing** : Domaines malveillants paraissent légitimes
- **HSTS bypass** : Contourner HTTP Strict Transport Security

### Protection contre IDN attacks

**Stratégies :**
1. **Affichage Punycode** : Montrer le domaine encodé
2. **Validation script** : Vérifier cohérence des scripts
3. **Listes blanches** : Domaines de confiance
4. **HSTS preload** : Liste de domaines HTTPS-only

**Exemple de validation :**
```python
import unicodedata
import re

def is_suspicious_domain(domain):
    # Extraire le nom de domaine (sans TLD)
    name = domain.split('.')[0]
    
    # Vérifier mélange de scripts
    scripts = set()
    for char in name:
        script = unicodedata.category(char)
        if script.startswith('L'):  # Lettre
            scripts.add(unicodedata.script(char))
    
    # Plus de 2 scripts différents = suspect
    if len(scripts) > 2:
        return True
    
    # Caractères de contrôle cachés
    if any(unicodedata.category(char).startswith('C') for char in name):
        return True
    
    return False
```

## Confusions visuelles

### Normalisation attacks

**Exemple :**
```
Nom légitime : login
Nom malveillant : login (mais avec des caractères spéciaux invisibles)
```

**Caractères invisibles :**
- **ZWSP** (U+200B) : Zero Width Space
- **ZWNJ** (U+200C) : Zero Width Non-Joiner
- **ZWJ** (U+200D) : Zero Width Joiner
- **Caractères de contrôle** : U+200E, U+200F (LRM, RLM)

### Attaques de contournement

**Bypass de validation :**
```javascript
// Validation faible
function isValidUsername(username) {
    return username.length >= 3 && /^[a-zA-Z0-9]+$/.test(username);
}

// Bypass possible
const malicious = "admin\u200B";  // Espace invisible
isValidUsername(malicious);  // true (mais visuellement "admin")
```

### Attaques sur mots de passe

**Mots de passe similaires :**
```
Mot de passe légitime : MySecurePass123
Mot de passe malveillant : MySecurePass123 (avec caractères invisibles)
```

**Exploitation :**
1. Utilisateur tape le mot de passe visible
2. Attaquant utilise la version avec caractères cachés
3. Système accepte les deux comme identiques

## Bonnes pratiques

### Défense en profondeur

**1. Normalisation obligatoire :**
```python
import unicodedata

def normalize_input(text):
    # NFKC : normalisation + compatibilité
    return unicodedata.normalize('NFKC', text)

# Appliquer à toutes les entrées utilisateur
username = normalize_input(username)
password = normalize_input(password)
```

**2. Validation de script :**
```python
import unicodedata

def validate_script_consistency(text):
    scripts = set()
    for char in text:
        if char.isalpha():
            scripts.add(unicodedata.script(char))
    
    # Un script principal maximum
    return len(scripts) <= 1

# Vérifier cohérence
if not validate_script_consistency(username):
    raise ValueError("Script mixte détecté")
```

**3. Liste noire de caractères :**
```python
# Caractères interdits
DANGEROUS_CHARS = {
    '\u200B',  # ZWSP
    '\u200C',  # ZWNJ
    '\u200D',  # ZWJ
    '\u200E',  # LRM
    '\u200F',  # RLM
    '\u2028',  # LS
    '\u2029',  # PS
}

def sanitize_text(text):
    return ''.join(char for char in text if char not in DANGEROUS_CHARS)
```

### Logging et monitoring

**Détection d'anomalies :**
```python
def log_suspicious_activity(text, context):
    # Vérifier caractères suspects
    suspicious_chars = []
    for char in text:
        category = unicodedata.category(char)
        if category in ['Cf', 'Cc']:  # Format/Control
            suspicious_chars.append(f"{char} (U+{ord(char):04X})")
    
    if suspicious_chars:
        logger.warning(f"Caractères suspects dans {context}: {suspicious_chars}")
```

### Tests de sécurité

**Tests automatisés :**
```python
def test_homoglyph_attacks():
    # Paires d'homoglyphes connus
    homoglyphs = [
        ('a', 'а'),  # latin vs cyrillique
        ('o', 'о'),
        ('p', 'р'),
        ('1', 'l'),  # un vs L minuscule
    ]
    
    for legit, malicious in homoglyphs:
        # Tester que le système les différencie
        assert legit != malicious
        assert normalize_input(legit) != normalize_input(malicious)
```

### Outils de protection

**Bibliothèques spécialisées :**
- **Unicode Security Mechanisms** (UTS #39)
- **Confusable Detection** : Détection d'homoglyphes
- **IDN Blacklist** : Domaines suspects
- **Browser Security** : Protection intégrée

**Configuration serveur :**
```nginx
# Nginx : bloquer caractères suspects
location /api/ {
    # Filtrer les requêtes avec caractères de contrôle
    if ($request_uri ~* [\x00-\x1F\x7F-\x9F]) {
        return 400;
    }
}
```

### Formation et sensibilisation

**Pour les développeurs :**
- Comprendre Unicode Security Considerations (UTS #39)
- Tester systématiquement l'internationalisation
- Auditer les entrées utilisateur
- Monitorer les tentatives d'exploitation

**Pour les utilisateurs :**
- Vérifier l'URL complète (pas seulement l'affichage)
- Utiliser des gestionnaires de mots de passe
- Être vigilant aux caractères suspects

Unicode apporte une richesse expressive formidable, mais nécessite une vigilance particulière en matière de sécurité pour éviter les attaques sophistiquées exploitant sa complexité.