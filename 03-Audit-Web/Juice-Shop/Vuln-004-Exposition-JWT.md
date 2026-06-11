# Vuln-004 — Exposition d'informations sensibles dans le JWT

## Gravité

**Moyenne**

**CVSS estimé : 5.5**

---

# Référence OWASP

* A02:2021 – Cryptographic Failures
* A07:2021 – Identification and Authentication Failures

---

# Description

Après authentification, l'application génère un jeton JWT (JSON Web Token) utilisé pour maintenir la session utilisateur.

L'analyse du jeton a révélé la présence de plusieurs informations sensibles directement accessibles côté client.

Bien que le JWT soit signé, son contenu est simplement encodé en Base64 et peut être décodé par tout utilisateur disposant du jeton.

---

# Endpoint concerné

```http
POST /rest/user/login
```

---

# Méthodologie

1. Exploitation de la vulnérabilité SQL Injection permettant l'obtention d'un compte administrateur.
2. Récupération du JWT renvoyé après authentification.
3. Décodage du jeton à l'aide de l'outil JWT.io.
4. Analyse du Header et du Payload.

---

# Résultats observés

## Header JWT

```json
{
  "typ": "JWT",
  "alg": "RS256"
}
```

### Observation

L'application utilise l'algorithme RS256, considéré comme robuste.

Aucune faiblesse cryptographique immédiate n'a été identifiée au niveau de la signature.

---

## Payload JWT

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

# Informations sensibles exposées

| Champ           | Sensibilité |
| --------------- | ----------- |
| id              | Moyenne     |
| email           | Moyenne     |
| role            | Élevée      |
| password (hash) | Élevée      |
| isActive        | Faible      |
| createdAt       | Faible      |
| updatedAt       | Faible      |

---

# Observation complémentaire

Le payload ne contient aucun champ d'expiration explicite :

```json
{
  "exp": "..."
}
```

L'absence d'une date d'expiration visible augmente potentiellement la durée de validité d'un jeton compromis.

---

# Impact

Un attaquant obtenant un JWT valide peut :

* Identifier les comptes privilégiés ;
* Connaître le rôle exact des utilisateurs ;
* Récupérer un hash de mot de passe ;
* Obtenir des informations internes sur les comptes ;
* Augmenter l'impact d'un vol de session.

Cette faiblesse devient particulièrement critique lorsqu'elle est combinée à une vulnérabilité XSS permettant le vol du JWT.

---

# Scénario d'attaque

```text
SQL Injection
        ↓
Obtention d'un accès administrateur
        ↓
Récupération du JWT
        ↓
Décodage du JWT
        ↓
Accès aux informations sensibles
        ↓
Vol potentiel de session via XSS
```

---

# Preuves

## Figure 1 — Décodage du Header JWT

```json
{
  "typ": "JWT",
  "alg": "RS256"
}
```

Capture :

```text
captures/JWT/jwt-header-decoded.png
```

---

## Figure 2 — Décodage du Payload JWT

Informations exposées :

```json
{
  "email": "admin@juice-sh.op",
  "role": "admin",
  "password": "0192023a7bbd73250516f069df18b500"
}
```

Capture :

```text
captures/JWT/jwt-payload-sensitive-data.png
```

---

# Recommandations

## Réduire le contenu du JWT

Conserver uniquement les informations strictement nécessaires :

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

---

## Ajouter une expiration

Ajouter les claims suivants :

```json
{
  "iat": "...",
  "nbf": "...",
  "exp": "..."
}
```

---

## Renforcer la gestion de session

Mettre en place :

* Rotation périodique des jetons ;
* Invalidation après déconnexion ;
* Durée de vie limitée ;
* Réauthentification pour les actions sensibles.

---

# Conclusion

Le mécanisme JWT utilise un algorithme de signature robuste (RS256), mais expose des informations sensibles inutiles côté client.

La présence du rôle administrateur, de l'adresse email et du hash du mot de passe dans le payload augmente significativement l'impact d'un vol de jeton.

Une politique de minimisation des données ainsi qu'une gestion plus stricte du cycle de vie des JWT sont recommandées.
