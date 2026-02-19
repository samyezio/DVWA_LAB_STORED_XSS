# XSS ver Samy — Analyse + DVWA XSS Lab

Mini-projet de compréhension et démonstration de **Cross-Site Scripting (XSS)**, avec une partie **théorie** (types, impacts, prévention) et une partie **pratique** sur **DVWA** (Metasploitable2 + Kali).

---

## 1) C’est quoi XSS ?
**XSS (Cross-Site Scripting)** est une vulnérabilité web qui permet à un attaquant d’**injecter du code côté client** (souvent JavaScript) dans une page web.  
Ce code s’exécute ensuite dans le navigateur d’autres utilisateurs, comme s’il provenait du site “de confiance”.

> Phrase mémo : **XSS = “le site fait exécuter mon script dans le navigateur de quelqu’un d’autre”.**

---

## 2) Pourquoi c’est grave ?
XSS sert surtout à **détourner une session** et à agir “comme” la victime :
- Vol de cookies / session (si cookies mal protégés)
- Actions au nom de l’utilisateur (usurpation)
- Vol de données visibles dans la page (DOM)
- Défiguration (defacement), redirections
- Requêtes HTTP en arrière-plan (`fetch`, `XMLHttpRequest`)
- Parfois abus d’APIs navigateur via social engineering (permissions)

---

## 3) Mécanisme (comment ça marche)
1. L’attaquant injecte un **payload** dans un champ (commentaire, profil, URL, etc.).
2. L’application ré-affiche ce contenu **sans échappement/encodage** → le navigateur l’interprète comme du code → exécution.

**Condition clé :** réinjection d’une entrée utilisateur dans un contexte HTML/JS/URL **sans protection**.

---

## 4) Les 3 types de XSS
### A) Reflected XSS (non persistante)
Payload dans la requête (souvent URL), renvoyé directement dans la réponse.

### B) Stored XSS (persistante)
Payload **stocké** (DB/profil/commentaire) et servi à **toutes les victimes** qui visitent la page.  
Souvent le plus dangereux car il s’exécute automatiquement pour chaque visiteur.

### C) DOM-Based XSS
Faille côté **JavaScript front** : le payload est injecté dans le DOM via des sinks dangereux (`innerHTML`, etc.), pas forcément via le serveur.

Notions liées : **Self-XSS** (arnaque console), **mXSS** (réécriture navigateur).

---

## 5) Cause racine
Le vrai problème = **mélanger données et code** : placer une donnée utilisateur dans un contexte HTML/JS/URL/CSS sans protection.

---

## 6) Prévention (défenses importantes)
- ✅ **Output encoding (priorité #1)** selon le contexte (HTML / attribut / JS / URL)
- Validation / filtrage (complément, pas suffisant seul)
- Sanitization HTML si HTML autorisé (whitelist stricte)
- Éviter sinks dangereux JS : `innerHTML`, `document.write`, `eval` → préférer `textContent`, templating sûr
- Frameworks modernes : souvent safe par défaut (attention aux exceptions)
- **CSP** (Content Security Policy) = défense en profondeur
- Cookies sécurisés : `HttpOnly`, `Secure`, `SameSite`
- WAF : utile mais jamais suffisant

---

## 7) Obfuscation (techniques de survie)
Objectif : contourner des filtres faibles (blacklists / mots-clés).
- **String splitting** : `'inne'+'rHTML'`, `'jav'+'a'`
- **String.fromCharCode** : générer guillemets/caractères à l’exécution
- **CSS “exécutable” (IE legacy)** : vieux comportements non standards
- **Conclusion défense :** éviter blacklists, utiliser encodage contextuel + sanitizer robuste + CSP

---

## 8) XSS Worm (auto-réplication & propagation)
Un ver XSS peut :
1. Lire le contexte (ex: `friendID`, token anti-CSRF)
2. Copier son propre code depuis la page (recherche d’un marqueur type `"mycode"`)
3. Ré-injecter le payload dans le profil de la victime
4. Déclencher des actions (ex: ajout automatique en ami)
5. Se propager en chaîne (effet exponentiel)

---

# Partie pratique — DVWA (Kali + Metasploitable2)

## 9) Objectifs du lab
1. Reproduire un **Stored XSS** sur DVWA (niveau **Low**) avec un payload inoffensif.
2. Appliquer un **correctif serveur** (output encoding) pour empêcher l’exécution.
3. Prouver avant/après avec captures d’écran.

## 10) Environnement
- **Kali Linux** : machine testeur (navigateur)
- **Metasploitable2** : machine cible (DVWA)
- Accès : `http://<IP_METASPLOITABLE>/dvwa`

## 11) Étapes — Reproduction Stored XSS
1. Ouvrir DVWA + login :
   - `admin / password`
2. **DVWA Security** → `Security Level = Low` → Submit
3. Aller dans **XSS (Stored)** (Guestbook)
4. Injecter un payload inoffensif :
   - `<script>alert('Stored XSS')</script>`
5. Recharger la page → popup = preuve d’exécution persistante

## 12) Correctif — Output Encoding (htmlspecialchars)
**Cause :** affichage brut du contenu utilisateur → le navigateur exécute les balises.  
**Fix :** encoder à l’affichage.

- Fichier : `dvwaPage.inc.php`
- Fonction : `dvwaGuestbook()`

**Avant (vulnérable)**

 `$name = $row[0];`
 `$comment = $row[1];`

**Après (sécurisé)**

 `$name    = htmlspecialchars($row[0], ENT_QUOTES, 'UTF-8');`
 `$comment = htmlspecialchars($row[1], ENT_QUOTES, 'UTF-8');`

Résultat attendu : le payload s’affiche en texte (&lt;script&gt;...) et ne s’exécute plus.

## 13) Conclusion

Le lab DVWA montre qu’un Stored XSS apparaît quand une application ré-affiche une entrée utilisateur sans encodage.
Le correctif principal est l’output encoding (ex: htmlspecialchars). En défense en profondeur : CSP, cookies sécurisés, validation serveur et réduction des sinks dangereux côté JavaScript.
