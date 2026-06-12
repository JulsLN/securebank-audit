# VULN-003 — Information Disclosure

## Référence

VULN-003

## Titre

Divulgation d'informations techniques

## Statut

Confirmée

## Gravité

Moyenne

## Score CVSS v3.1

5.3 MEDIUM

### Vecteur CVSS

```text
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N
```


# Classification

## OWASP Top 10

A05:2021 – Security Misconfiguration

## CWE

CWE-209 – Information Exposure Through an Error Message


# Description

L'application retourne des messages d'erreur détaillés contenant des informations internes sur son fonctionnement.

Ces informations permettent à un attaquant de mieux comprendre l'architecture applicative et les composants utilisés.


# Informations divulguées

## Framework

```text
Express ^4.22.1
```


## JWT

```text
Invalid token: no header in signature
```


## OpenSSL

```text
error:1E08010C:DECODER routines::unsupported
```


## Exceptions JavaScript

```text
Cannot read properties of undefined (reading 'length')
```


# Preuves

### JWT invalide

```http
Authorization: Bearer abc
```

Réponse :

```text
UnauthorizedError: Invalid token: no header in signature
```


### JWT malformé

Réponse :

```text
Cannot read properties of undefined
```


### Endpoint Complaints

Réponse :

```text
error:1E08010C:DECODER routines::unsupported
```


# Impact

Ces informations permettent :

* fingerprinting technologique ;
* préparation d'attaques ciblées ;
* identification des bibliothèques utilisées ;
* compréhension des mécanismes internes.


# Risque métier

Cette vulnérabilité facilite les phases de reconnaissance et augmente la probabilité d'exploitation d'autres failles.


# Recommandations

Remplacer les messages détaillés par :

```text
Authentication failed
```

ou

```text
Internal server error
```

Mettre en place :

* gestion centralisée des erreurs ;
* journalisation côté serveur ;
* suppression des stack traces côté client.


# Audits associés

* AUD-003-Analyse-Authentification
* AUD-005-Analyse-Erreurs


# Conclusion

L'application divulgue des informations techniques internes via ses messages d'erreur, facilitant la phase de reconnaissance d'un attaquant.
