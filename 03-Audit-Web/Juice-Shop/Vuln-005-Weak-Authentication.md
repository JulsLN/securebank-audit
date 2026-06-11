# Vuln-005 — Absence de protection contre les attaques par force brute

## Gravité

**Moyenne**

---

## Référence OWASP

- A07:2021 – Identification and Authentication Failures

---

## Endpoint concerné

```http
POST /rest/user/login
```

---

## Description

Le mécanisme d'authentification ne semble pas implémenter de protection efficace contre les tentatives répétées de connexion.

Plusieurs tentatives de connexion invalides ont été envoyées successivement sans déclencher de mécanisme de défense observable.

---

## Méthodologie

L'outil Burp Suite Intruder a été utilisé afin d'automatiser une série de tentatives d'authentification avec des mots de passe incorrects.

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

---

## Résultats observés

Chaque requête a retourné :

```http
HTTP/1.1 401 Unauthorized
```

Aucun des mécanismes suivants n'a été observé :

- CAPTCHA ;
- limitation du débit (Rate Limiting) ;
- verrouillage temporaire du compte ;
- blocage d'adresse IP ;
- augmentation progressive du délai de réponse.

---

## Impact

Un attaquant pourrait automatiser :

- des attaques par force brute ;
- des attaques de type Password Spraying ;
- des attaques de type Credential Stuffing.

L'absence de limitation augmente considérablement les chances de compromission des comptes utilisant des mots de passe faibles ou réutilisés.

---

## Preuves

### Figure 1 — Test Intruder

Burp Suite Intruder a envoyé plusieurs tentatives d'authentification invalides successives.

Résultat :

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

Aucun mécanisme de protection n'a été déclenché.

Capture :

```text
captures/Juice-Shop/Weak-Authentication/intruder-bruteforce-test.png
```

---

## Recommandations

Mettre en œuvre :

### Rate Limiting

Exemple :

```text
5 tentatives par minute et par adresse IP
```

### Verrouillage temporaire

Exemple :

```text
Blocage de 15 minutes après 5 échecs consécutifs
```

### CAPTCHA adaptatif

Déclenchement après plusieurs tentatives échouées.

### MFA

Activer l'authentification multi-facteurs pour les comptes sensibles.

### Surveillance

Journaliser et alerter les tentatives répétées d'authentification.

---

## Conclusion

Aucune protection contre les tentatives répétées d'authentification n'a été observée durant les tests.

Cette faiblesse augmente le risque d'attaques automatisées contre les comptes utilisateurs et doit être corrigée afin de renforcer la sécurité du mécanisme d'authentification.