# VULN-005 — Absence de protection contre les attaques par force brute

## Référence

VULN-005

## Titre

Absence de protection contre les attaques par force brute

## Statut

Confirmée

## Gravité

Moyenne

## Score CVSS v3.1

6.5 MEDIUM

### Vecteur CVSS

```text
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:L/A:L
```


# Classification

## OWASP Top 10

A07:2021 – Identification and Authentication Failures

## CWE

CWE-307 – Improper Restriction of Excessive Authentication Attempts


# Description

Le mécanisme d'authentification exposé par l'application ne semble pas implémenter de protection efficace contre les tentatives répétées de connexion.

Les tests réalisés ont montré qu'un nombre important de requêtes d'authentification invalides pouvait être envoyé sans déclencher de mesure défensive observable.

L'absence de limitation permet à un attaquant d'automatiser des tentatives de connexion à grande échelle contre les comptes utilisateurs.


# Endpoint affecté

```http
POST /rest/user/login
```


# Contexte technique

L'application utilise un mécanisme d'authentification basé sur :

```text
Email + Mot de passe
JWT (JSON Web Token)
```

Aucune mesure anti-automatisation n'a été observée lors des tests.


# Méthodologie

Les tests ont été réalisés à l'aide de :

* Burp Suite Community Edition ;
* Burp Intruder ;
* Curl.

L'objectif était d'évaluer la résistance du mécanisme d'authentification face à des tentatives répétées de connexion.


# Procédure de test

Une requête d'authentification valide a été interceptée dans Burp Suite.

Le paramètre :

```json
{
  "email":"audit@securebank.local",
  "password":"XXXXX"
}
```

a été utilisé comme point d'injection dans Burp Intruder.

Une liste de mots de passe incorrects a ensuite été envoyée automatiquement.

Payloads utilisés :

```text
aaaa
bbbb
cccc
dddd
eeee
ffff
gggg
hhhh
iiii
jjjj
```


# Résultats observés

Chaque requête a retourné :

```http
HTTP/1.1 401 Unauthorized
```

Exemple :

```json
{
  "error":"Invalid email or password."
}
```

ou équivalent selon le contexte applicatif.


# Mécanismes recherchés

Les contrôles suivants ont été spécifiquement recherchés :

## CAPTCHA

Non observé.


## Rate Limiting

Non observé.


## Blocage temporaire de compte

Non observé.


## Blocage d'adresse IP

Non observé.


## Délai progressif

Non observé.


## Challenge d'authentification supplémentaire

Non observé.


# Preuves

## Figure 1 — Burp Intruder

Résultat observé :

```text
401
401
401
401
401
401
401
401
401
401
...
```

Aucune variation de comportement n'a été détectée.

Capture :

```text
captures/Juice-Shop/Weak-Authentication/intruder-bruteforce-test.png
```


# Impact

Un attaquant peut automatiser :

## Brute Force

Tentative successive de nombreux mots de passe pour un compte donné.


## Password Spraying

Utilisation d'un même mot de passe faible sur plusieurs comptes.

Exemple :

```text
Password123
Summer2025
Welcome1
```


## Credential Stuffing

Réutilisation d'identifiants compromis issus d'autres fuites de données.


# Risque métier

Conséquences potentielles :

* compromission de comptes utilisateurs ;
* accès non autorisé aux données personnelles ;
* fraude ;
* usurpation d'identité ;
* atteinte à la réputation de l'organisation.

Dans un contexte réel, cette faiblesse augmente considérablement les probabilités de succès d'une attaque automatisée.


# Facteurs aggravants

Les risques deviennent particulièrement élevés si :

* des mots de passe faibles sont utilisés ;
* les utilisateurs réutilisent leurs mots de passe ;
* aucune MFA n'est déployée ;
* les journaux d'authentification ne sont pas surveillés.


# Recommandations

## Limitation du débit

Mettre en place une politique de limitation :

```text
5 tentatives par minute et par adresse IP
```

ou

```text
10 tentatives par utilisateur sur 15 minutes
```


## Verrouillage temporaire

Exemple :

```text
Blocage du compte pendant 15 minutes après 5 échecs consécutifs
```


## CAPTCHA adaptatif

Déclenchement après plusieurs tentatives échouées.


## Authentification multifacteur

Activer une MFA pour :

* administrateurs ;
* comptes sensibles ;
* utilisateurs privilégiés.


## Surveillance

Détecter automatiquement :

* les pics de tentatives ;
* les Password Spraying ;
* les Credential Stuffing.


## Journalisation

Conserver :

* adresse IP ;
* horodatage ;
* compte ciblé ;
* résultat de l'authentification.


# Références

OWASP Authentication Cheat Sheet

OWASP Credential Stuffing Prevention Cheat Sheet

OWASP Multifactor Authentication Cheat Sheet

NIST SP 800-63B


# Audits associés

* AUD-003-Analyse-Authentification


# Conclusion

Les tests ont démontré l'absence de mécanisme de protection efficace contre les tentatives répétées d'authentification.

Cette faiblesse permet l'automatisation d'attaques de type Brute Force, Password Spraying et Credential Stuffing et augmente significativement le risque de compromission des comptes utilisateurs.
