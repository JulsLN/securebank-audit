# Rapport d'Audit de Sécurité – SecureBank

## Informations générales

**Projet :** SecureBank
**Type de mission :** Audit de sécurité applicative (Web Application Security Assessment)
**Auditeur :** Julien L.
**Date :** Juin 2026
**Environnement audité :** OWASP Juice Shop (environnement de laboratoire pédagogique)

---

# 1. Objectifs de la mission

L'objectif principal de cet audit est d'évaluer le niveau de sécurité de l'application cible afin d'identifier les vulnérabilités susceptibles d'être exploitées par un attaquant.

Les objectifs spécifiques sont :

* Cartographier la surface d'attaque.
* Identifier les composants exposés.
* Analyser les mécanismes d'authentification.
* Tester les fonctionnalités applicatives critiques.
* Évaluer les risques associés aux vulnérabilités découvertes.
* Fournir des recommandations de remédiation.

---

# 2. Méthodologie

La mission a été réalisée selon une méthodologie inspirée des référentiels :

* OWASP Testing Guide
* OWASP Top 10 – 2021
* PTES (Penetration Testing Execution Standard)

Les phases réalisées sont :

```text
01 - Cadrage
        ↓
02 - Reconnaissance
        ↓
03 - Audit Web
        ↓
Analyse des vulnérabilités
        ↓
Restitution
```

---

# 3. Phase de reconnaissance

## 3.1 Identification de la cible

La phase de reconnaissance a permis d'identifier :

* Le système exposé
* Les services accessibles
* Les technologies utilisées
* Les composants applicatifs disponibles

### Preuves associées

* detection-des-ports.png
* detection-systeme-exploitation.png
* detection-des-versions.png

---

## 3.2 Analyse technologique

Les outils suivants ont été utilisés :

### WhatWeb

Objectif :

* Identifier les technologies web
* Détecter les frameworks
* Détecter les composants serveur

Preuve :

* Analyse-WhatWeb.png

---

### Gobuster

Objectif :

* Énumération de répertoires
* Découverte de ressources cachées

Preuve :

* Commande-Gobuster.png

---

### Nikto

Objectif :

* Détection de mauvaises configurations
* Recherche de composants vulnérables

Preuve :

* Commande-Nikto.png

---

## 3.3 Cartographie de la surface d'attaque

L'analyse de l'application a permis d'identifier :

### Fonctions critiques

* Authentification
* Recherche de produits
* Gestion des utilisateurs
* Gestion du panier
* API REST

### Points d'entrée identifiés

```text
/rest/user/login
/rest/products/search
/rest/user/whoami
/rest/basket
```

### Preuves

* Cartographie-surface-attaque.png
* Parametres-API.png
* Enumeration-HTTP.png

---

# 4. Audit Web

## Vuln-001 — SQL Injection Authentication Bypass

### Gravité

**Critique**

### Référence OWASP

A03:2021 – Injection

---

### Description

Le formulaire d'authentification est vulnérable à une injection SQL permettant le contournement complet du mécanisme de connexion.

Un attaquant peut obtenir un accès administrateur sans posséder d'identifiants valides.

---

### Endpoint concerné

```http
POST /rest/user/login
```

---

### Reproduction

#### Test de validation

```json
{
  "email":"' ",
  "password":"test"
}
```

Résultat :

```http
HTTP/1.1 500 Internal Server Error
```

Erreur observée :

```text
SQLITE_ERROR
SequelizeDatabaseError
```

---

#### Payload d'exploitation

```json
{
  "email":"' OR 1=1--",
  "password":"test"
}
```

Résultat :

```http
HTTP/1.1 200 OK
```

JWT valide obtenu.

Compte compromis :

```text
admin@juice-sh.op
```

---

### Impact

* Contournement de l'authentification
* Accès administrateur
* Escalade de privilèges
* Compromission complète de l'application

---

### Preuves

* SQLi-error.png
* SQLi-auth-bypass.png
* SQLi-admin-jwt.png

---

## Vuln-002 — Cross-Site Scripting (DOM XSS)

### Gravité

**Élevée**

### Référence OWASP

A03:2021 – Injection

---

### Description

La fonctionnalité de recherche réinjecte des données utilisateur dans le DOM sans neutralisation suffisante.

Cela permet l'exécution de code JavaScript arbitraire dans le navigateur de l'utilisateur.

---

### Fonctionnalité concernée

Recherche de produits

---

### Payloads validés

#### Payload n°1

```html
<img src=x onerror=alert(1)>
```

Résultat :

Exécution d'une fenêtre JavaScript.

---

#### Payload n°2

```html
<iframe src="javascript:alert(1)">
```

Résultat :

Exécution de code JavaScript.

---

### Impact

* Vol de session
* Vol de JWT
* Exécution d'actions au nom de l'utilisateur
* Défiguration de l'interface

---

### Preuves

* XSS-img-onerror.png
* XSS-iframe.png

---

## Vuln-003 — Exposition d'informations sensibles dans le JWT

### Gravité

**Moyenne**

### Référence OWASP

A02:2021 – Cryptographic Failures

---

### Description

Le JWT retourné après authentification contient plusieurs informations sensibles qui ne devraient pas être exposées au client.

---

### Header observé

```json
{
  "typ": "JWT",
  "alg": "RS256"
}
```

---

### Payload observé

```json
{
  "id": 1,
  "email": "admin@juice-sh.op",
  "password": "0192023a7bbd73250516f069df18b500",
  "role": "admin",
  "isActive": true
}
```

---

### Informations exposées

| Champ           | Sensibilité |
| --------------- | ----------- |
| id              | Moyenne     |
| email           | Moyenne     |
| role            | Élevée      |
| password (hash) | Élevée      |
| isActive        | Faible      |

---

### Impact

* Divulgation d'informations sensibles
* Facilitation d'attaques ultérieures
* Augmentation de l'impact d'un vol de JWT

---

### Preuves

* JWT-header.png
* JWT-payload.png

---

# 5. Analyse du risque

## Synthèse

| Vulnérabilité | Impact     | Probabilité | Criticité |
| ------------- | ---------- | ----------- | --------- |
| SQL Injection | Très élevé | Très élevée | Critique  |
| XSS           | Élevé      | Élevée      | Élevée    |
| JWT           | Moyen      | Moyenne     | Moyenne   |

---

## Chaîne d'attaque observée

Les vulnérabilités découvertes peuvent être combinées :

```text
SQL Injection
        ↓
Obtention d'un compte administrateur
        ↓
Récupération d'un JWT valide
        ↓
Décodage du JWT
        ↓
Accès aux informations sensibles
        ↓
Exploitation XSS
        ↓
Vol potentiel de session
```

Cette chaîne démontre une compromission complète de l'environnement applicatif.

---

# 6. Conclusion

L'audit de sécurité réalisé sur l'environnement SecureBank a permis d'identifier plusieurs vulnérabilités critiques affectant directement les mécanismes d'authentification et la sécurité applicative.

La vulnérabilité SQL Injection identifiée constitue le risque le plus important et permet une compromission complète de l'application.

La présence simultanée d'une vulnérabilité XSS et d'une exposition excessive d'informations dans les JWT augmente significativement la surface d'attaque et l'impact potentiel d'une compromission.

La correction des vulnérabilités identifiées doit être considérée comme prioritaire avant toute mise en production d'un environnement présentant des caractéristiques similaires.
