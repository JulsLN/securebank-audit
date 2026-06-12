# VULN-001 — Broken Object Level Authorization (BOLA)

## Référence

VULN-001

## Titre

Broken Object Level Authorization (BOLA)

## Statut

Confirmée

## Gravité

Élevée

## Score CVSS v3.1

8.1 HIGH

### Vecteur CVSS

```text
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:L/A:N
```


# Classification

## OWASP API Security Top 10

API1:2023 – Broken Object Level Authorization

## CWE

CWE-639 – Authorization Bypass Through User-Controlled Key


# Description

L'application permet à un utilisateur authentifié d'accéder à des ressources appartenant à d'autres utilisateurs simplement en manipulant l'identifiant de l'objet demandé.

Le contrôle d'autorisation côté serveur ne vérifie pas que la ressource consultée appartient à l'utilisateur authentifié.


# Ressource affectée

```http
GET /api/Feedbacks/{id}
```


# Preuve

Compte utilisé :

```text
audit@securebank.local
```

JWT valide obtenu après authentification.

Requête :

```http
GET /api/Feedbacks/1
Authorization: Bearer <JWT>
```

Réponse :

```json
{
  "UserId": 1,
  "id": 1,
  "comment": "...",
  "rating": 5
}
```

L'utilisateur connecté possède :

```text
UserId = 24
```

La ressource retournée appartient à :

```text
UserId = 1
```


# Impact

Un attaquant authentifié peut :

* consulter les ressources d'autres utilisateurs ;
* contourner les restrictions métier ;
* accéder à des données confidentielles.


# Risque métier

La vulnérabilité compromet directement :

* la confidentialité des données ;
* l'isolation entre utilisateurs ;
* la conformité réglementaire.


# Recommandations

Vérifier systématiquement :

```text
resource.owner_id == authenticated_user.id
```

Mettre en œuvre :

* contrôle d'accès objet par objet ;
* middleware centralisé d'autorisation ;
* tests automatiques d'autorisation.


# Audits associés

* AUD-003-Analyse-Authentification
* AUD-004-Analyse-Autorisation


# Conclusion

L'application présente une vulnérabilité de type BOLA permettant à un utilisateur authentifié d'accéder aux ressources d'autres utilisateurs sans autorisation appropriée.
