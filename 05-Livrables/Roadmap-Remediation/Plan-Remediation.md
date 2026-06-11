# Plan de Remédiation – SecureBank

## Objectif

Ce document présente les actions correctives recommandées suite à l'audit de sécurité réalisé sur l'environnement SecureBank.

Les mesures proposées sont classées par ordre de priorité afin de réduire efficacement les risques identifiés.

---

# Synthèse des vulnérabilités

| Référence | Vulnérabilité                                   | Criticité |
| --------- | ----------------------------------------------- | --------- |
| Vuln-001  | SQL Injection Authentication Bypass             | Critique  |
| Vuln-002  | Cross-Site Scripting (DOM XSS)                  | Élevée    |
| Vuln-003  | Exposition d'informations sensibles dans le JWT | Moyenne   |

---

# Priorisation des actions

## Priorité 1 – Critique (0 à 7 jours)

### Vuln-001 — SQL Injection Authentication Bypass

### Risque

Cette vulnérabilité permet à un attaquant non authentifié de contourner totalement le mécanisme de connexion et d'obtenir un accès administrateur.

### Impact

* Compromission complète de l'application
* Escalade de privilèges
* Accès aux données sensibles
* Contrôle des fonctions administratives

### Actions correctives

#### Développement

* Utiliser exclusivement des requêtes préparées (Prepared Statements).
* Interdire toute concaténation de données utilisateur dans les requêtes SQL.
* Utiliser les mécanismes de paramétrage natifs de l'ORM Sequelize.
* Mettre en place une validation stricte des entrées utilisateur.

#### Sécurité

* Supprimer les messages d'erreur SQL visibles par l'utilisateur.
* Journaliser les erreurs uniquement côté serveur.
* Mettre en place une surveillance des tentatives d'authentification anormales.

#### Qualité

* Ajouter des tests unitaires de sécurité.
* Intégrer des tests automatisés contre les injections SQL dans la CI/CD.

### Résultat attendu

* Suppression complète de la possibilité d'injection SQL.
* Renforcement du mécanisme d'authentification.

---

# Priorité 2 – Élevée (7 à 30 jours)

## Vuln-002 — Cross-Site Scripting (DOM XSS)

### Risque

Un attaquant peut exécuter du code JavaScript arbitraire dans le navigateur d'un utilisateur.

### Impact

* Vol de session
* Vol de JWT
* Usurpation d'identité
* Défiguration de l'application

### Actions correctives

#### Développement

Remplacer :

```javascript
element.innerHTML = userInput
```

Par :

```javascript
element.textContent = userInput
```

ou utiliser les mécanismes de sanitisation fournis par le framework.

#### Validation

* Filtrer les caractères spéciaux dangereux.
* Encoder les données affichées dans l'interface.

#### Durcissement navigateur

Mettre en place une Content Security Policy (CSP) :

```http
Content-Security-Policy:
default-src 'self';
script-src 'self';
object-src 'none';
frame-src 'none';
```

#### Tests

* Vérifier les formulaires.
* Vérifier les paramètres URL.
* Vérifier les réponses API réinjectées dans le DOM.

### Résultat attendu

* Suppression des possibilités d'exécution de JavaScript arbitraire.
* Réduction significative du risque de vol de session.

---

# Priorité 3 – Moyenne (30 à 60 jours)

## Vuln-003 — Exposition d'informations sensibles dans le JWT

### Risque

Le JWT contient des informations inutiles côté client et augmente l'impact d'un vol de session.

### Informations exposées

* email
* role
* password (hash)
* createdAt
* updatedAt
* informations internes

### Actions correctives

#### Réduction du contenu du JWT

Conserver uniquement :

```json
{
  "sub": "1",
  "role": "admin",
  "exp": 1781170000
}
```

Supprimer :

```text
password
createdAt
updatedAt
profileImage
lastLoginIp
deluxeToken
```

#### Gestion de session

Ajouter :

```json
{
  "iat": "...",
  "nbf": "...",
  "exp": "..."
}
```

#### Politique de sécurité

* Rotation périodique des jetons.
* Invalidation après déconnexion.
* Durée de vie limitée.
* Réauthentification pour les actions sensibles.

### Résultat attendu

* Réduction de l'exposition d'informations sensibles.
* Limitation de l'impact d'un vol de JWT.

---

# Feuille de route 30 / 60 / 90 jours

## J+30

Objectifs :

* Correction de la SQL Injection.
* Suppression des erreurs SQL visibles.
* Déploiement des requêtes préparées.

Indicateur :

```text
0 vulnérabilité critique ouverte
```

---

## J+60

Objectifs :

* Correction des vulnérabilités XSS.
* Déploiement d'une Content Security Policy.
* Validation des entrées utilisateur.

Indicateur :

```text
0 vulnérabilité élevée ouverte
```

---

## J+90

Objectifs :

* Durcissement complet de la gestion JWT.
* Revue de code sécurité.
* Mise en place des contrôles de sécurité dans le cycle de développement.

Indicateur :

```text
Réduction durable de la surface d'attaque
```

---

# Recommandations stratégiques

## Développement sécurisé

Mettre en place un processus Secure SDLC incluant :

* Revue de code sécurité.
* Validation des dépendances.
* Tests automatisés de sécurité.
* Analyse SAST.
* Analyse DAST.

---

## Sensibilisation

Former les développeurs aux risques :

* OWASP Top 10
* SQL Injection
* XSS
* Gestion sécurisée des sessions
* Bonnes pratiques JWT

---

## Contrôles réguliers

Réaliser :

* Des audits de sécurité périodiques.
* Des revues de configuration.
* Des tests d'intrusion annuels.

---

# Conclusion

L'audit réalisé dans le cadre du projet SecureBank a permis d'identifier une vulnérabilité critique, une vulnérabilité élevée et une faiblesse de sécurité de niveau moyen.

La correction immédiate de la SQL Injection doit être considérée comme la priorité absolue.

La mise en œuvre progressive des actions décrites dans ce plan permettra de réduire significativement la surface d'attaque et d'améliorer durablement la posture de sécurité globale de l'application.

Le niveau de risque résiduel pourra être considéré comme acceptable une fois l'ensemble des mesures de remédiation appliquées et validées.
