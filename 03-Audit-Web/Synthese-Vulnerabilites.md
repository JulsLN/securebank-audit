# Synthèse des Vulnérabilités – SecureBank

## Objectif

Ce document présente une vue consolidée des vulnérabilités identifiées lors de l'audit de sécurité de l'application SecureBank.

Les vulnérabilités sont classées selon leur niveau de criticité et leur impact potentiel sur la sécurité du système.

---

# Résumé Exécutif

L'audit a permis d'identifier plusieurs vulnérabilités affectant :

* L'authentification ;
* La gestion des sessions ;
* Le traitement des entrées utilisateur ;
* Les mécanismes de sécurité applicative.

Au total :

| Niveau        | Nombre |
| ------------- | ------ |
| Critique      | 1      |
| Élevée        | 1      |
| Moyenne       | 2      |
| Faible        | 0      |
| Non confirmée | 1      |

Le niveau de risque global est évalué comme :

# CRITIQUE

La présence d'une vulnérabilité SQL Injection permettant l'obtention d'un accès administrateur constitue le risque principal observé.

---

# Matrice des vulnérabilités

| Référence | Vulnérabilité                                             | Criticité     | Statut    |
| --------- | --------------------------------------------------------- | ------------- | --------- |
| Vuln-001  | SQL Injection Authentication Bypass                       | Critique      | Confirmée |
| Vuln-002  | DOM-Based Cross-Site Scripting (XSS)                      | Élevée        | Confirmée |
| Vuln-003  | IDOR (Broken Access Control)                              | Non confirmée | Testée    |
| Vuln-004  | Exposition d'informations sensibles dans le JWT           | Moyenne       | Confirmée |
| Vuln-005  | Absence de protection contre les attaques par force brute | Moyenne       | Confirmée |

---

# Vulnérabilités confirmées

## Vuln-001 — SQL Injection Authentication Bypass

### Criticité

**Critique**

### Description

Le formulaire d'authentification est vulnérable à une injection SQL permettant le contournement complet du mécanisme de connexion.

### Impact

* Obtention d'un compte administrateur ;
* Escalade de privilèges ;
* Accès aux données sensibles ;
* Compromission complète de l'application.

### Référence

```text
Vuln-001-SQLi.md
```

---

## Vuln-002 — DOM-Based Cross-Site Scripting (XSS)

### Criticité

**Élevée**

### Description

La fonctionnalité de recherche permet l'exécution de code JavaScript arbitraire dans le navigateur de l'utilisateur.

### Impact

* Vol de session ;
* Vol de JWT ;
* Exécution d'actions au nom de l'utilisateur ;
* Défiguration de l'application.

### Référence

```text
Vuln-002-XSS.md
```

---

## Vuln-004 — Exposition d'informations sensibles dans le JWT

### Criticité

**Moyenne**

### Description

Le JWT retourné après authentification contient plusieurs informations sensibles qui ne devraient pas être accessibles côté client.

### Informations exposées

* Adresse email administrative ;
* Rôle utilisateur ;
* Hash du mot de passe ;
* Métadonnées internes.

### Impact

* Divulgation d'informations sensibles ;
* Facilitation d'attaques ciblées ;
* Augmentation de l'impact d'un vol de session.

### Référence

```text
Vuln-004-JWT.md
```

---

## Vuln-005 — Absence de protection contre les attaques par force brute

### Criticité

**Moyenne**

### Description

Le mécanisme d'authentification ne met pas en œuvre de protection visible contre les tentatives répétées de connexion.

### Observations

Les tests réalisés avec Burp Suite Intruder n'ont révélé :

* Aucun verrouillage de compte ;
* Aucun CAPTCHA ;
* Aucun blocage IP ;
* Aucun mécanisme de Rate Limiting.

### Impact

* Brute Force ;
* Password Spraying ;
* Credential Stuffing.

### Référence

```text
Vuln-005-Weak-Authentication.md
```

---

# Vulnérabilités testées mais non confirmées

## Vuln-003 — IDOR (Broken Access Control)

### Statut

**Non confirmée**

### Description

Plusieurs endpoints ont été analysés afin de rechercher un accès non autorisé à des ressources appartenant à d'autres utilisateurs.

### Endpoints testés

```http
/rest/user/whoami
/rest/basket
/api/Users
```

### Résultat

Aucune preuve suffisante n'a permis de démontrer un accès non autorisé aux données d'un autre utilisateur.

### Conclusion

Aucune vulnérabilité IDOR n'a été confirmée durant la mission.

### Référence

```text
Vuln-003-IDOR.md
```

---

# Chaîne d'attaque observée

Les vulnérabilités confirmées peuvent être combinées afin d'augmenter significativement le niveau de risque.

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
Exploitation potentielle via XSS
        ↓
Vol de session
```

Cette chaîne démontre qu'un attaquant pourrait compromettre totalement l'application.

---

# Priorisation des risques

| Priorité | Vulnérabilité                       | Niveau   |
| -------- | ----------------------------------- | -------- |
| P1       | SQL Injection Authentication Bypass | Critique |
| P2       | DOM-Based XSS                       | Élevée   |
| P3       | JWT Information Disclosure          | Moyenne  |
| P4       | Weak Authentication Controls        | Moyenne  |

---

# Conclusion

L'audit de sécurité réalisé sur l'environnement SecureBank a permis d'identifier quatre vulnérabilités confirmées affectant directement les mécanismes d'authentification, la gestion des sessions et la sécurité applicative.

La vulnérabilité SQL Injection constitue le risque principal et doit être corrigée en priorité.

La combinaison des vulnérabilités observées démontre qu'un attaquant pourrait obtenir un accès administrateur, récupérer des informations sensibles et compromettre les sessions utilisateur.

La mise en œuvre des recommandations présentées dans le plan de remédiation permettra de réduire significativement la surface d'attaque et d'améliorer durablement la posture de sécurité globale du système.
