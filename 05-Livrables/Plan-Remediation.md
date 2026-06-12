# Plan de Remédiation – SecureBank

## Objectif

Ce document présente les mesures correctives recommandées suite aux vulnérabilités identifiées lors de l'audit de sécurité de l'environnement SecureBank.

Les actions sont classées selon leur niveau de criticité afin de permettre une réduction rapide du risque global.


# Synthèse des vulnérabilités

| Référence | Vulnérabilité | Criticité |
|------------|-------------|------------|
| VULN-004 | SQL Injection Authentication Bypass | Critique |
| VULN-006 | DOM-Based Cross-Site Scripting (XSS) | Élevée |
| VULN-001 | Broken Object Level Authorization (BOLA / IDOR) | Élevée |
| VULN-002 | Broken Function Level Authorization (BFLA) | Élevée |
| VULN-003 | Information Disclosure | Moyenne |
| VULN-005 | Absence de protection contre les attaques par force brute | Moyenne |


# Priorité 1 – Critique (0 à 7 jours)

## VULN-004 — SQL Injection Authentication Bypass

### Références

**OWASP Top 10**

- A03:2021 – Injection

**CWE**

- CWE-89 – SQL Injection


### Risque

Cette vulnérabilité permet à un attaquant non authentifié de contourner complètement le mécanisme d'authentification et d'obtenir un accès administrateur.


### Impact

- Compromission complète de l'application ;
- Escalade de privilèges ;
- Accès aux données sensibles ;
- Contrôle des fonctionnalités administratives.


### Actions correctives

#### Développement sécurisé

Utiliser exclusivement :

- Requêtes préparées ;
- Paramètres liés (Bind Variables) ;
- ORM configuré correctement.

Interdire :

```sql
SELECT * FROM Users WHERE email = '" + email + "'
```

Utiliser :

```sql
SELECT * FROM Users WHERE email = ?
```


#### Validation des entrées

Mettre en place :

- Validation stricte des formats ;
- Contrôle côté serveur ;
- Rejet des entrées non conformes.


#### Gestion des erreurs

Supprimer les messages détaillés :

```text
SQLITE_ERROR
SequelizeDatabaseError
Stack Trace
```

Retourner uniquement :

```text
Une erreur est survenue.
```


### Résultat attendu

Les payloads suivants ne doivent plus produire aucun effet :

```sql
' OR 1=1--
' UNION SELECT
```

Suppression complète du risque d'injection SQL.


# Priorité 2 – Élevée (7 à 30 jours)

## VULN-006 — DOM-Based Cross-Site Scripting (XSS)

### Références

**OWASP Top 10**

- A03:2021 – Injection

**CWE**

- CWE-79 – Cross-Site Scripting


### Risque

Exécution de code JavaScript arbitraire dans le navigateur des utilisateurs.


### Impact

- Vol de session ;
- Vol de JWT ;
- Exécution d'actions au nom de l'utilisateur ;
- Défiguration de l'application.


### Actions correctives

#### Développement sécurisé

Remplacer :

```javascript
element.innerHTML = userInput
```

par :

```javascript
element.textContent = userInput
```


#### Encodage des sorties

Encoder systématiquement :

```text
<
>
"
'
&
```

avant affichage.


#### Content Security Policy (CSP)

Déployer :

```http
Content-Security-Policy:
default-src 'self';
script-src 'self';
object-src 'none';
frame-src 'none';
```


#### Validation des entrées

Contrôler :

- Paramètres URL ;
- Formulaires ;
- Réponses API injectées dans le DOM.


### Résultat attendu

Les payloads suivants ne doivent plus être exécutés :

```html
<img src=x onerror=alert(1)>
```

```html
<iframe src="javascript:alert(1)">
```


# Priorité 2 – Élevée (7 à 30 jours)

## VULN-001 — Broken Object Level Authorization (BOLA / IDOR)

### Références

**OWASP API Security Top 10**

- API1:2023 – Broken Object Level Authorization

**CWE**

- CWE-639 – Authorization Bypass Through User-Controlled Key


### Risque

Un utilisateur authentifié peut accéder à des ressources appartenant à d'autres utilisateurs en modifiant simplement un identifiant dans les requêtes API.


### Impact

- Violation de confidentialité ;
- Accès non autorisé aux données ;
- Fuite d'informations métier ;
- Exposition de données utilisateurs.


### Actions correctives

#### Contrôle d'accès côté serveur

Vérifier systématiquement :

```text
resource.ownerId == authenticatedUser.id
```

avant toute consultation ou modification.


#### Validation des ressources

Implémenter :

- Contrôle de propriété ;
- Contrôle d'appartenance ;
- Journalisation des accès refusés.


#### Principe du moindre privilège

Limiter les accès aux seules ressources appartenant à l'utilisateur authentifié.


### Résultat attendu

Les requêtes suivantes doivent être refusées lorsqu'elles ciblent une ressource appartenant à un autre utilisateur :

```http
GET /api/Feedbacks/1
GET /api/Feedbacks/2
```


# Priorité 2 – Élevée (7 à 30 jours)

## VULN-002 — Broken Function Level Authorization (BFLA)

### Références

**OWASP API Security Top 10**

- API5:2023 – Broken Function Level Authorization

**CWE**

- CWE-862 – Missing Authorization


### Risque

Certaines fonctionnalités sont accessibles sans contrôle d'autorisation approprié.


### Impact

- Accès à des fonctions non prévues ;
- Contournement des contrôles métiers ;
- Escalade horizontale ou verticale de privilèges.


### Actions correctives

#### Contrôle RBAC

Mettre en place :

- Contrôle basé sur les rôles ;
- Vérification systématique des privilèges ;
- Validation des autorisations avant exécution.


#### Séparation des privilèges

Définir clairement :

```text
Anonymous
Customer
Administrator
```

et les permissions associées.


#### Revue des endpoints

Auditer l'ensemble des routes sensibles afin de vérifier la cohérence des contrôles d'autorisation.


### Résultat attendu

Toutes les fonctions sensibles doivent être protégées par des contrôles d'autorisation explicites.


# Priorité 3 – Moyenne (30 à 60 jours)

## VULN-003 — Information Disclosure

### Références

**OWASP Top 10**

- A01:2021 – Broken Access Control

**CWE**

- CWE-200 – Information Exposure


### Risque

L'application expose des informations techniques et fonctionnelles facilitant la reconnaissance par un attaquant.


### Impact

- Cartographie accélérée de l'application ;
- Identification des technologies utilisées ;
- Préparation d'attaques ciblées ;
- Réduction de l'effort de reconnaissance.


### Actions correctives

#### Réduction des informations exposées

Supprimer :

- Versions des composants ;
- Messages d'erreur détaillés ;
- Informations internes inutiles ;
- Données sensibles dans les réponses API.


#### Durcissement des réponses HTTP

Masquer :

```text
Stack traces
Messages de debug
Informations framework
```


#### Principe du moindre divulgation

Retourner uniquement les informations strictement nécessaires aux utilisateurs.


### Résultat attendu

Aucune information sensible ou technique exploitable ne doit être exposée dans les réponses applicatives.


# Priorité 3 – Moyenne (30 à 60 jours)

## VULN-005 — Absence de protection contre les attaques par force brute

### Références

**OWASP Top 10**

- A07:2021 – Identification and Authentication Failures

**CWE**

- CWE-307 – Improper Restriction of Excessive Authentication Attempts


### Risque

Le mécanisme d'authentification accepte un nombre important de tentatives sans mécanisme de protection visible.


### Impact

- Brute Force ;
- Password Spraying ;
- Credential Stuffing ;
- Compromission de comptes faibles.


### Actions correctives

#### Rate Limiting

Limiter :

```text
5 tentatives par minute et par adresse IP
```


#### Verrouillage temporaire

Exemple :

```text
Blocage pendant 15 minutes après 5 échecs consécutifs
```


#### CAPTCHA adaptatif

Déclenchement automatique après plusieurs tentatives échouées.


#### MFA

Déployer l'authentification multi-facteurs pour :

- Administrateurs ;
- Comptes privilégiés ;
- Opérations sensibles.


#### Surveillance

Détecter et journaliser :

- Brute Force ;
- Password Spraying ;
- Credential Stuffing.

Déclencher des alertes de sécurité.


### Résultat attendu

Réduction significative du risque d'attaques automatisées contre les comptes utilisateurs.


# Feuille de route 30 / 60 / 90 jours

## J+30

Objectifs :

- Correction SQL Injection ;
- Correction XSS ;
- Correction BOLA ;
- Correction BFLA.

Indicateur :

```text
0 vulnérabilité Critique ouverte
0 vulnérabilité Élevée ouverte
```


## J+60

Objectifs :

- Réduction de l'exposition d'informations ;
- Mise en œuvre du durcissement des réponses ;
- Déploiement du Rate Limiting ;
- Mise en place du verrouillage temporaire.

Indicateur :

```text
0 vulnérabilité Moyenne ouverte
```


## J+90

Objectifs :

- Déploiement MFA ;
- Déploiement CSP ;
- Intégration SAST ;
- Intégration DAST ;
- Revue de code sécurité.

Indicateur :

```text
Réduction significative du risque résiduel
```


# Recommandations stratégiques

## Secure SDLC

Intégrer systématiquement :

- Revue de code sécurité ;
- Analyse SAST ;
- Analyse DAST ;
- Contrôle des dépendances ;
- Tests automatisés de sécurité.


## Sensibilisation

Former les équipes aux risques :

- SQL Injection ;
- XSS ;
- Contrôles d'accès ;
- Authentification ;
- OWASP Top 10 ;
- OWASP API Security Top 10.


## Audits réguliers

Mettre en place :

- Audits annuels ;
- Revues de configuration ;
- Tests d'intrusion périodiques ;
- Exercices Red Team / Blue Team.


# Conclusion

L'audit de sécurité SecureBank a permis d'identifier plusieurs vulnérabilités affectant directement les mécanismes d'authentification, les contrôles d'accès et la sécurité applicative.

La correction immédiate de la vulnérabilité SQL Injection constitue la priorité absolue en raison du risque de compromission complète de l'application.

La mise en œuvre progressive des actions décrites dans ce document permettra de réduire significativement la surface d'attaque et d'améliorer durablement la posture de sécurité globale de l'environnement audité.