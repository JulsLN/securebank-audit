# VULN-002 — Broken Function Level Authorization (BFLA)

## Référence

VULN-002

## Titre

Broken Function Level Authorization

## Statut

Confirmée

## Gravité

Élevée

## Score CVSS v3.1

8.8 HIGH

### Vecteur CVSS

```text
CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:L
```


# Classification

## OWASP API Security Top 10

API5:2023 – Broken Function Level Authorization

## CWE

CWE-285 – Improper Authorization


# Description

L'application autorise un utilisateur standard à exécuter une opération de suppression sur une ressource appartenant à un autre utilisateur.

Aucun contrôle de privilège n'est appliqué avant l'exécution de l'action.


# Ressource affectée

```http
DELETE /api/Feedbacks/{id}
```


# Preuve

Requête :

```http
DELETE /api/Feedbacks/1
Authorization: Bearer <JWT>
```

Réponse :

```http
HTTP/1.1 200 OK
```

```json
{
  "status":"success",
  "data":{}
}
```

Vérification :

```http
GET /api/Feedbacks
```

Le feedback supprimé n'est plus présent.


# Impact

Un attaquant authentifié peut :

* supprimer des données ;
* modifier l'intégrité applicative ;
* perturber les opérations métiers.


# Risque métier

Conséquences potentielles :

* perte de données ;
* fraude ;
* altération des journaux utilisateurs ;
* indisponibilité partielle du service.


# Recommandations

Mettre en œuvre :

* contrôle de rôle ;
* contrôle de propriété ;
* validation systématique des privilèges avant exécution.

Exemple :

```text
ROLE_ADMIN
ou
resource.owner_id == current_user.id
```


# Audits associés

* AUD-004-Analyse-Autorisation


# Conclusion

L'absence de contrôle d'autorisation sur les fonctions sensibles permet à un utilisateur standard d'exécuter des actions destructrices normalement réservées aux propriétaires ou aux administrateurs.
