# Vuln-003 — Exposition d'informations sensibles dans le JWT

## Gravité

**Moyenne**

**CVSS estimé : 5.3 – 6.5**

---

## Catégorie OWASP

- A01:2021 – Broken Access Control (impact indirect)
- A02:2021 – Cryptographic Failures
- A07:2021 – Identification and Authentication Failures

---

## Endpoint concerné

```http
POST /rest/user/login
```

---

## Description

Après authentification, l'application génère un jeton JWT (JSON Web Token) contenant de nombreuses informations sensibles relatives à l'utilisateur connecté.

L'analyse du token révèle que celui-ci expose des données internes qui ne sont pas nécessaires au fonctionnement du mécanisme d'authentification côté client.

Toute personne ayant accès au jeton peut décoder son contenu sans posséder la clé privée utilisée pour la signature.

---

## Méthodologie

1. Authentification sur l'application.
2. Récupération du JWT renvoyé par l'API.
3. Décodage du token à l'aide de l'outil JWT.io.
4. Analyse des informations contenues dans le payload.

---

## Résultat observé

### Header

```json
{
  "typ": "JWT",
  "alg": "RS256"
}
```

L'algorithme utilisé (RS256) est robuste et correctement reconnu.

### Payload

```json
{
  "status": "success",
  "data": {
    "id": 1,
    "email": "admin@juice-sh.op",
    "password": "0192023a7bbd73250516f069df18b500",
    "role": "admin",
    "isActive": true,
    "createdAt": "2026-06-10 08:00:05.367 +00:00",
    "updatedAt": "2026-06-10 08:00:05.367 +00:00"
  },
  "iat": 1781165319
}
```

---

## Informations sensibles exposées

Le JWT contient les éléments suivants :

| Information | Sensibilité |
|------------|------------|
| id | Moyenne |
| email | Moyenne |
| role | Élevée |
| password (hash) | Élevée |
| createdAt | Faible |
| updatedAt | Faible |
| isActive | Faible |

L'exposition du champ :

```json
{
  "password": "0192023a7bbd73250516f069df18b500"
}
```

constitue une mauvaise pratique de sécurité, même lorsqu'il s'agit d'un hash.

---

## Observation complémentaire

Aucun champ d'expiration explicite n'a été identifié dans le payload :

```json
{
  "exp": "..."
}
```

L'absence d'expiration visible peut favoriser la réutilisation prolongée d'un jeton compromis.

---

## Impact

Un attaquant obtenant un JWT valide peut :

- Identifier les comptes privilégiés ;
- Connaître le rôle exact des utilisateurs ;
- Récupérer des informations internes de l'application ;
- Réaliser des attaques de corrélation ou de brute force hors ligne sur les hashes exposés ;
- Exploiter une vulnérabilité XSS afin de dérober les jetons de session.

### Chaîne d'attaque observée

```text
SQL Injection
        ↓
Obtention du compte administrateur
        ↓
Récupération du JWT
        ↓
Décodage du JWT
        ↓
Accès aux informations sensibles
```

---

## Preuves

### Capture 1

Décodage du Header JWT :

```json
{
  "typ": "JWT",
  "alg": "RS256"
}
```

Fichier :

```text
captures/jwt/jwt-header.png
```

### Capture 2

Décodage du Payload JWT :

```json
{
  "email": "admin@juice-sh.op",
  "role": "admin",
  "password": "0192023a7bbd73250516f069df18b500"
}
```

Fichier :

```text
captures/jwt/jwt-payload.png
```

---

## Recommandations

### Réduire les informations contenues dans le JWT

Conserver uniquement les données strictement nécessaires :

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
```

### Ajouter une expiration

Ajouter les claims suivants :

```json
{
  "exp": "...",
  "iat": "...",
  "nbf": "..."
}
```

### Renforcer la gestion de session

- Rotation périodique des jetons ;
- Durée de vie limitée ;
- Invalidation côté serveur après déconnexion ;
- Protection contre le vol de jeton via CSP et sécurisation contre les attaques XSS.

---

## Conclusion

L'analyse du JWT n'a pas révélé de faiblesse cryptographique dans l'algorithme utilisé (RS256), mais a mis en évidence une exposition excessive d'informations sensibles dans le payload du jeton.

Cette pratique augmente l'impact potentiel d'un vol de session ou d'une vulnérabilité XSS et doit être corrigée selon le principe de minimisation des données.