# Chapitre 15 — HKEY et Windows Registry

## Introduction

Sous Windows, le **Registry** constitue la base de données centrale où sont stockés tous les paramètres système, incluant les configurations linguistiques et d'encodage. Comprendre sa structure et son utilisation est crucial pour maîtriser la gestion du texte sous Windows.

Ce chapitre explore l'architecture du Registry et son rôle dans la gestion des locales et encodages.

## Qu'est-ce que le Windows Registry ?

### Architecture générale

Le Registry Windows est une **base de données hiérarchique** stockant :
- **Paramètres système**
- **Configurations utilisateurs**
- **Informations matérielles**
- **Préférences applications**

**Structure :**
```
Computer
├── HKEY_CLASSES_ROOT (HKCR)
├── HKEY_CURRENT_USER (HKCU)
├── HKEY_LOCAL_MACHINE (HKLM)
├── HKEY_USERS (HKU)
└── HKEY_CURRENT_CONFIG (HKCC)
```

### Persistence et performance

**Avantages :**
- **Centralisé** : Un seul endroit pour toutes les configurations
- **Hiérarchique** : Organisation logique
- **Performant** : Accès rapide en mémoire
- **Transactionnel** : Modifications atomiques

**Inconvénients :**
- **Complexe** : Structure profonde et verbeuse
- **Fragile** : Corruption possible
- **Legacy** : Concepts anciens persistants

## HKEY_LOCAL_MACHINE

### Clés principales pour le texte

**HKLM\SYSTEM\CurrentControlSet\Control\Nls :**
```
CodePage
├── ACP                   # ANSI Code Page (1252)
├── OEMCP                 # OEM Code Page (437)
└── Language              # Configuration langue
```

**ACP (ANSI Code Page) :**
- Définit l'encodage pour les applications non-Unicode
- Par défaut : 1252 (Latin-1 Windows)
- Hérité des anciens systèmes

**OEMCP (OEM Code Page) :**
- Utilisé pour la console Windows
- Par défaut : 437 (DOS US)
- Nécessaire pour les outils console legacy

### Configuration des langues

**HKLM\SYSTEM\CurrentControlSet\Control\MUI\UILanguages :**
```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\MUI\UILanguages]
    "en-US"=dword:00000001
    "fr-FR"=dword:00000001
```

**HKLM\SYSTEM\CurrentControlSet\Control\Keyboard Layouts :**
- Configuration des dispositions clavier
- Support multilingue
- Raccourcis de changement de langue

## HKEY_CURRENT_USER

### Préférences utilisateur

**HKCU\Control Panel\International :**
```reg
[HKEY_CURRENT_USER\Control Panel\International]
    "Locale"="0000040c"              # fr-FR (1036 décimal)
    "LocaleName"="fr-FR"
    "sCountry"="France"
    "sLanguage"="FRA"
    "sShortDate"="dd/MM/yyyy"
    "sLongDate"="dddd d MMMM yyyy"
    "sShortTime"="HH:mm"
    "sTimeFormat"="HH:mm:ss"
    "sDecimal"=","                   # Virgule décimale
    "sThousand"=" "                  # Espace milliers
    "iDigits"="2"
    "iNegCurr"="8"
    "sCurrency"="€"
    "sMonDecimalSep"=","
    "sMonThousandSep"=" "
    "iMeasure"="0"                   # Métrique
```

### Configuration des applications

**HKCU\Software\Microsoft\Notepad :**
```reg
[HKEY_CURRENT_USER\Software\Microsoft\Notepad]
    "lfFaceName"="Consolas"
    "iPointSize"=110
    "fSaveWindowPositions"=dword:00000001
    "fWrap"=dword:00000000
```

**Encodage par défaut :**
```reg
[HKEY_CURRENT_USER\Software\Microsoft\Notepad]
    "iDefaultEncoding"=1  # 1=ANSI, 2=UTF-16LE, 3=UTF-16BE, 4=UTF-8
```

## Code pages, locales, fonts

### Table de correspondance

**Locales Windows :**
```
0x0409 : en-US (Anglais US)
0x040c : fr-FR (Français France)
0x0407 : de-DE (Allemand Allemagne)
0x0410 : it-IT (Italien Italie)
0x0c0a : es-ES (Espagnol Espagne)
```

**Format :**
- **16 bits** : Langue (haut) + Pays (bas)
- **Compatible** : Avec les anciens systèmes Windows

### Configuration des polices

**HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\EUDC :**
- **EUDC** : End User Defined Characters
- Polices personnalisées pour caractères asiatiques

**HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\SharedDLLs :**
- Chemins des DLL de polices
- Versions des composants de rendu

## Encodage et système Windows

### Évolution historique

**Windows 9x/ME :**
- Code pages natives
- Support limité Unicode
- Conversions nécessaires

**Windows NT/2000 :**
- Unicode natif en interne
- Conversions ANSI ↔ Unicode
- API A/W (ANSI/Wide)

**Windows XP/Vista :**
- UTF-8 pour certaines APIs
- Support émoji limité

**Windows 10+ :**
- UTF-8 comme option système
- Support complet Unicode
- Émoji et symboles modernes

### API de conversion

**Windows utilise des fonctions de conversion :**

```cpp
// Conversion ANSI → Unicode
int MultiByteToWideChar(
    UINT CodePage,      // CP_ACP, CP_UTF8, etc.
    DWORD dwFlags,
    LPCCH lpMultiByteStr,
    int cbMultiByte,
    LPWSTR lpWideCharStr,
    int cchWideChar
);

// Conversion Unicode → ANSI
int WideCharToMultiByte(
    UINT CodePage,
    DWORD dwFlags,
    LPCWCH lpWideCharStr,
    int cchWideChar,
    LPSTR lpMultiByteStr,
    int cbMultiByte,
    LPCCH lpDefaultChar,
    LPBOOL lpUsedDefaultChar
);
```

### Problèmes de conversion

**Perte de données :**
```cpp
// Caractère Unicode → ANSI impossible
WCHAR unicode_char = L'€';  // U+20AC
char ansi_buffer[10];
int result = WideCharToMultiByte(CP_ACP, 0, &unicode_char, 1,
                                  ansi_buffer, sizeof(ansi_buffer),
                                  "?", NULL);
// Result: '?' (caractère de remplacement)
```

**Solutions :**
- **Utiliser UTF-8** : Supporte tous les caractères Unicode
- **Validation** : Vérifier les conversions
- **Fallback** : Caractères de remplacement appropriés

## Configuration moderne

### Windows 10 UTF-8

**Depuis Windows 10 version 1903 :**
```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\CodePage]
    "ACP"="65001"      # UTF-8
    "OEMCP"="65001"    # UTF-8 aussi
```

**Impact :**
- Console utilise UTF-8
- Applications héritées voient UTF-8 comme ANSI
- Meilleure compatibilité internationale

### Applications et Registry

**Stockage des paramètres :**
```cpp
// Lecture depuis Registry
HKEY hKey;
RegOpenKeyEx(HKEY_CURRENT_USER, L"Software\\MyApp", 0, KEY_READ, &hKey);

WCHAR buffer[256];
DWORD size = sizeof(buffer);
RegQueryValueEx(hKey, L"Language", NULL, NULL, (LPBYTE)buffer, &size);

RegCloseKey(hKey);
```

**Configuration multilingue :**
```reg
[HKEY_CURRENT_USER\Software\MyApp]
    "InstallLanguage"="fr-FR"
    "DisplayLanguage"="fr-FR"
    "HelpLanguage"="en-US"
```

## Bonnes pratiques

### Gestion des clés Registry

**1. Droits d'accès appropriés :**
```cpp
// Lecture seule pour HKLM
RegOpenKeyEx(HKEY_LOCAL_MACHINE, L"path", 0, KEY_READ, &hKey);

// Écriture pour HKCU
RegOpenKeyEx(HKEY_CURRENT_USER, L"path", 0, KEY_WRITE, &hKey);
```

**2. Gestion d'erreurs :**
```cpp
LONG result = RegOpenKeyEx(...);
if (result != ERROR_SUCCESS) {
    // Gestion d'erreur appropriée
    switch (result) {
        case ERROR_FILE_NOT_FOUND: // Clé inexistante
        case ERROR_ACCESS_DENIED:  // Droits insuffisants
        // ...
    }
}
```

**3. Nettoyage :**
```cpp
RegCloseKey(hKey);  // Toujours fermer les handles
```

### Migration legacy

**Applications anciennes :**
- Détecter l'encodage automatiquement
- Convertir vers Unicode en interne
- Stocker en UTF-8 dans Registry

**Configuration :**
```reg
[HKEY_CURRENT_USER\Software\MyApp]
    "TextEncoding"="UTF-8"
    "LegacySupport"=dword:00000001
```

### Debugging

**Outils de diagnostic :**
```cmd
REM Voir les clés de langue
reg query "HKCU\Control Panel\International"

REM Voir les code pages
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Nls\CodePage"
```

**Logs d'application :**
```cpp
// Logger les problèmes d'encodage
void LogEncodingIssue(const WCHAR* text, UINT codepage) {
    WCHAR msg[512];
    swprintf(msg, L"Encoding issue: %s (CP: %d)", text, codepage);
    OutputDebugString(msg);
}
```

Le Registry Windows constitue le cœur de la configuration système, particulièrement pour la gestion des langues et encodages dans les applications modernes.