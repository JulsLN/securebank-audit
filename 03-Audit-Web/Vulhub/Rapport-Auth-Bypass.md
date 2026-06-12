# Rapport Auth Bypass – Next.js CVE-2025-29927

## 1. Contexte

Dans le cadre du projet SecureBank, un environnement Vulhub basé sur Next.js a été déployé afin d'étudier une vulnérabilité de contournement d'authentification affectant certaines versions du framework Next.js.

L'objectif de cet audit est d'identifier les mécanismes de contrôle d'accès implémentés dans l'application et d'évaluer les risques liés à une mauvaise gestion des middlewares de sécurité.

---

## 2. Reconnaissance

### Vérification du service

Commande utilisée :

```bash
curl -I http://10.0.0.200:3000
```

Résultat :

```http
HTTP/1.1 307 Temporary Redirect
Location: /login
```

### Analyse

La page racine redirige automatiquement les utilisateurs non authentifiés vers une interface de connexion.

Cette redirection indique la présence d'un mécanisme de contrôle d'accès.

---

### Identification technologique

Commande utilisée :

```bash
whatweb http://10.0.0.200:3000
```

Résultat :

```text
X-Powered-By[Next.js]
Title[Admin Dashboard]
```

### Analyse

L'application repose sur le framework Next.js.

Plusieurs en-têtes spécifiques au framework sont également présents :

```text
x-nextjs-cache
x-nextjs-prerender
x-nextjs-stale-time
```

---

### Scan Nmap

Commande utilisée :

```bash
nmap -Pn -sV 10.0.0.200 -p 3000
```

Résultat :

```text
3000/tcp open
```

### Analyse

Le service web est accessible sur le port TCP 3000.

---

## 3. Cartographie Applicative

### Ressources identifiées

| Ressource | Description |
|------------|-------------|
| / | Racine de l'application |
| /login | Interface d'authentification |
| Dashboard | Interface d'administration |

### Interface d'authentification

URL :

```text
http://10.0.0.200:3000/login
```

Fonctionnalités observées :

- Champ Username
- Champ Password
- Bouton Login

Identifiants de démonstration :

```text
admin / password
```

### Dashboard Administrateur

Après authentification, l'utilisateur accède à l'interface d'administration.

Fonctionnalités observées :

- Users
- Content
- Settings

### Contrôle d'accès observé

La racine de l'application est protégée par une redirection vers `/login`.

Le dashboard affiche explicitement qu'il s'agit d'une ressource protégée réservée aux utilisateurs authentifiés.

---

## 4. Analyse Technique

### Description de la vulnérabilité

La CVE-2025-29927 affecte certaines versions du framework Next.js utilisant des middlewares pour appliquer les contrôles d'accès.

Les middlewares sont exécutés avant le traitement des requêtes et permettent notamment :

- l'authentification ;
- l'autorisation ;
- les redirections ;
- certains contrôles de sécurité.

### Constat

L'application auditée utilise :

- une page de connexion ;
- un dashboard administrateur ;
- un mécanisme de redirection ;
- un middleware de contrôle d'accès.

### Surface d'attaque

Les composants sensibles identifiés sont :

- Dashboard administrateur ;
- Gestion des utilisateurs ;
- Gestion du contenu ;
- Paramètres système.

### Risque identifié

Une vulnérabilité affectant le middleware peut conduire à un accès non autorisé à des ressources normalement réservées aux utilisateurs authentifiés.

---

## 5. Impact

Une exploitation réussie peut permettre :

- l'accès à des ressources protégées ;
- le contournement des contrôles d'accès ;
- la consultation de données sensibles ;
- l'exécution d'actions réservées aux administrateurs.

### Évaluation du risque

| Critère | Niveau |
|----------|---------|
| Confidentialité | Élevée |
| Intégrité | Élevée |
| Disponibilité | Faible |
| Criticité globale | Élevée |

---

## 6. Recommandations

### Correctifs

- Mettre à jour Next.js vers une version corrigée.
- Vérifier l'application correcte des correctifs de sécurité.

### Durcissement

- Ne pas reposer uniquement sur les middlewares.
- Implémenter des vérifications côté serveur.
- Ajouter des contrôles d'autorisation complémentaires.
- Limiter l'exposition des interfaces d'administration.

### Supervision

- Journaliser les accès aux zones protégées.
- Surveiller les tentatives d'accès non authentifiées.
- Détecter les comportements anormaux.

---

## 7. Conclusion

L'analyse de l'environnement Vulhub a permis d'identifier une application Next.js exposée sur le port 3000 et reposant sur un mécanisme de contrôle d'accès basé sur un middleware.

La présence d'un dashboard administrateur, d'une page d'authentification et de ressources protégées confirme l'existence d'un contrôle d'accès centralisé.

La vulnérabilité CVE-2025-29927 démontre l'importance de ne pas dépendre exclusivement des middlewares pour sécuriser les ressources sensibles et justifie une remédiation prioritaire des versions vulnérables du framework.
