# Synthèse des Vulnérabilités – Projet SecureBank

## Objectif

Ce document présente une vue consolidée des vulnérabilités identifiées lors de l'audit de sécurité réalisé sur l'environnement SecureBank.

Les vulnérabilités sont classées selon leur criticité et leur impact potentiel sur la confidentialité, l'intégrité et la disponibilité du système.

---

# Résumé Exécutif

L'audit a permis d'identifier plusieurs vulnérabilités affectant différents composants de l'application :

* Authentification
* Gestion des sessions
* Traitement des entrées utilisateur
* Services exposés
* Mécanismes d'échange de données

Le niveau de risque global est évalué comme :

# CRITIQUE

La présence d'une SQL Injection permettant l'obtention d'un compte administrateur constitue le risque principal identifié.

---

# Matrice de Synthèse

| Référence   | Vulnérabilité                                   | Catégorie OWASP                 | Gravité       | Statut        |
| ----------- | ----------------------------------------------- | ------------------------------- | ------------- | ------------- |
| Vuln-001    | SQL Injection Authentication Bypass             | A03 – Injection                 | Critique      | Confirmée     |
| Vuln-002    | DOM-Based XSS                                   | A03 – Injection                 | Élevée        | Confirmée     |
| Vuln-003    | IDOR                                            | Broken Access Control           | Non démontrée | Non confirmée |
| Vuln-004    | Exposition d'informations sensibles dans le JWT | A02 – Cryptographic Failures    | Moyenne       | Confirmée     |
| SSRF        | Server-Side Request Forgery                     | A10 – SSRF                      | À compléter   | À évaluer     |
| XXE         | XML External Entity Injection                   | A05 – Security Misconfiguration | À compléter   | À évaluer     |
| Auth-Bypass | Contournement d'authentification                | Broken Authentication           | À compléter   | À évaluer     |

---

# Détail des vulnérabilités confirmées

## Vuln-001 — SQL Injection Authentication Bypass

### Gravité

**Critique**

### Description

Le point d'entrée d'authentification est vulnérable à une injection SQL permettant de contourner totalement le mécanisme de connexion.

### Impact

* Obtention d'un accès administrateur
* Escalade de privilèges
* Compromission complète de l'application
* Accès aux données sensibles

### Preuves

```text
Vuln-001-SQLi.md
preuves/SQLi/
```

---

## Vuln-002 — DOM-Based Cross-Site Scripting

### Gravité

**Élevée**

### Description

La fonctionnalité de recherche permet l'exécution de code JavaScript arbitraire dans le navigateur de l'utilisateur.

### Impact

* Vol de session
* Vol de JWT
* Exécution d'actions au nom de l'utilisateur
* Défiguration de l'application

### Preuves

```text
Vuln-002-XSS.md
preuves/XSS/
```

---

## Vuln-004 — Exposition d'informations sensibles dans le JWT

### Gravité

**Moyenne**

### Description

Le JWT expose plusieurs informations sensibles qui ne devraient pas être accessibles au client.

### Informations exposées

* Adresse email administrative
* Rôle administrateur
* Hash du mot de passe
* Métadonnées internes

### Impact

* Divulgation d'informations sensibles
* Augmentation de l'impact d'un vol de session
* Facilitation d'attaques ciblées

### Preuves

```text
Vuln-004-JWT.md
preuves/JWT/
```

---

# Vulnérabilités non confirmées

## Vuln-003 — IDOR

### Statut

Non confirmée durant la mission.

### Observations

Plusieurs endpoints ont été analysés :

```text
/rest/user/whoami
/rest/basket
```

Aucune preuve suffisante n'a permis de démontrer un accès non autorisé aux ressources d'un autre utilisateur.

### Recommandation

Maintenir les contrôles d'autorisation côté serveur et poursuivre les tests lors d'audits futurs.

---

# Chaîne d'attaque observée

Les vulnérabilités identifiées peuvent être combinées pour accroître significativement le niveau de risque.

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

Cette chaîne démontre une compromission complète de l'application.

---

# Priorités de remédiation

| Priorité | Vulnérabilité | Délai recommandé |
| -------- | ------------- | ---------------- |
| P1       | SQL Injection | Immédiat         |
| P2       | XSS           | < 30 jours       |
| P3       | JWT           | < 60 jours       |

---

# Conclusion

L'audit a permis d'identifier deux vulnérabilités majeures directement exploitables ainsi qu'une faiblesse de conception dans la gestion des JWT.

La vulnérabilité SQL Injection représente le risque le plus critique et doit être corrigée en priorité.

La combinaison des vulnérabilités observées démontre qu'un attaquant pourrait compromettre l'intégralité de l'application et accéder à des informations sensibles sans autorisation.

La mise en œuvre du plan de remédiation permettra de réduire significativement la surface d'attaque et d'améliorer durablement la posture de sécurité de l'environnement SecureBank.
