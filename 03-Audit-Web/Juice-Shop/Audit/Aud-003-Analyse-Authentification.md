# AUD-003 — Analyse du mécanisme d'authentification

## Informations générales

**Date :** 12/06/2026

**Auditeur :** Équipe Audit

**Application :** OWASP Juice Shop

**Cible :**

```text
http://10.10.10.2:3000
```


# Objectif

Évaluer la robustesse du mécanisme d'authentification exposé par l'application.

Les tests ont porté sur :

* la gestion des erreurs ;
* la validation des JWT ;
* la résistance aux tentatives de force brute ;
* la protection contre l'automatisation ;
* la divulgation d'informations sensibles lors de l'authentification.


# Méthodologie

Les analyses ont été réalisées à l'aide des outils suivants :

* Burp Suite Community Edition ;
* Burp Intruder ;
* Curl ;
* Navigateur Firefox.

Les tests ont été réalisés sur le point d'entrée :

```http
POST /rest/user/login
```

et sur différents endpoints protégés par JWT.


# Test 1 — Validation du comportement sans jeton

## Objectif

Déterminer le comportement de l'application lorsqu'une ressource protégée est sollicitée sans authentification.

## Requête

```bash
curl -i \
http://10.10.10.2:3000/api/Users
```

## Résultat

```http
HTTP/1.1 401 Unauthorized
```

Message retourné :

```text
UnauthorizedError: No Authorization header was found
```

## Observation

Le mécanisme de contrôle d'accès empêche l'accès aux ressources protégées en l'absence de jeton.

## Conclusion

Le contrôle d'authentification est appliqué.


# Test 2 — Validation d'un JWT invalide

## Objectif

Évaluer le comportement de l'application face à un jeton malformé.

## Requête

```bash
curl -i \
-H "Authorization: Bearer abc" \
http://10.10.10.2:3000/api/Users
```

## Résultat

```http
HTTP/1.1 401 Unauthorized
```

Erreur retournée :

```text
UnauthorizedError: Invalid token: no header in signature 'abc'
```

## Observation

L'application divulgue des détails internes concernant le processus de validation JWT.

## Risque

Cette divulgation facilite la compréhension du mécanisme d'authentification par un attaquant.

## Vulnérabilité associée

VULN-003 — Information Disclosure


# Test 3 — Validation d'un jeton incomplet

## Objectif

Observer la gestion des erreurs pour un JWT partiellement valide.

## Requête

```bash
curl -i \
-H "Authorization: Bearer <JWT_tronqué>" \
http://10.10.10.2:3000/api/Users
```

## Résultat

```http
HTTP/1.1 401 Unauthorized
```

Erreur observée :

```text
Cannot read properties of undefined (reading 'length')
```

## Observation

Une exception interne est exposée à l'utilisateur.

## Risque

Divulgation d'informations techniques sur l'implémentation.

## Vulnérabilité associée

VULN-003 — Information Disclosure


# Test 4 — Analyse de la résistance aux attaques par force brute

## Objectif

Déterminer si le mécanisme d'authentification implémente des protections contre les tentatives répétées de connexion.


## Endpoint testé

```http
POST /rest/user/login
```


## Méthodologie

Burp Suite Intruder a été utilisé afin d'automatiser plusieurs tentatives de connexion successives avec des mots de passe incorrects.

Exemples de payloads utilisés :

```text
aaaa
bbbb
cccc
dddd
eeee
ffff
gggg
```


## Résultats observés

Chaque tentative a retourné :

```http
HTTP/1.1 401 Unauthorized
```

Aucun mécanisme de protection n'a été observé :

* CAPTCHA ;
* limitation du débit ;
* verrouillage temporaire du compte ;
* blocage d'adresse IP ;
* augmentation progressive du délai de réponse.


## Analyse

L'application accepte un nombre important de tentatives d'authentification invalides sans mettre en œuvre de mécanisme de défense visible.

Cette situation permet l'automatisation d'attaques :

* Brute Force ;
* Password Spraying ;
* Credential Stuffing.


## Impact

La compromission de comptes utilisant des mots de passe faibles ou réutilisés devient significativement plus probable.


## Preuves

### Figure 1 — Test Burp Intruder

Résultat observé :

```text
401
401
401
401
401
401
401
...
```

Capture :

```text
captures/Juice-Shop/Weak-Authentication/intruder-bruteforce-test.png
```


## Vulnérabilité associée

VULN-005 — Absence de protection contre les attaques par force brute


# Synthèse

Les contrôles d'authentification sont présents mais plusieurs faiblesses ont été identifiées :

| Référence | Vulnérabilité                               | Niveau |
| --------- | ------------------------------------------- | ------ |
| VULN-003  | Information Disclosure                      | Moyen  |
| VULN-005  | Absence de protection contre la force brute | Moyen  |


# Recommandations

## Protection contre la force brute

Mettre en œuvre :

### Rate Limiting

```text
5 tentatives par minute et par adresse IP
```

### Verrouillage temporaire

```text
Blocage du compte pendant 15 minutes après 5 échecs consécutifs
```

### CAPTCHA adaptatif

Déclenchement après plusieurs tentatives échouées.

### MFA

Activation de l'authentification multi-facteurs pour les comptes sensibles.

### Journalisation

Détection et alertes sur les tentatives répétées d'authentification.


# Conclusion

Les mécanismes d'authentification empêchent l'accès aux ressources protégées sans jeton valide.

Cependant, plusieurs faiblesses ont été identifiées, notamment une divulgation excessive d'informations techniques et l'absence de protection efficace contre les attaques automatisées de type force brute.

Ces vulnérabilités doivent être corrigées afin d'améliorer la robustesse globale du système d'authentification.
