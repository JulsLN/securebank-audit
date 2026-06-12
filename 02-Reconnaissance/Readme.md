# Niveau 1 - Reconnaissance et Cartographie

## Objectif

Identifier les technologies utilisées, les points d'entrée et les mécanismes de sécurité présents sur l'application.

## Technologies identifiées

- Express.js
- Node.js
- JWT
- API REST

## Headers observés

- X-Frame-Options: SAMEORIGIN
- X-Content-Type-Options: nosniff

## Authentification

Mécanisme basé sur JWT.

Stockage :

- LocalStorage
- Cookie token

## Contrôles d'accès

Les endpoints suivants nécessitent un JWT :

- /api/Users
- /api/BasketItems
- /api/Addresss

## Vulnérabilités découvertes

### V-01 Information Disclosure

L'application divulgue :

- version du framework
- structure interne
- chemins système

### V-02 User Enumeration

Un utilisateur standard peut récupérer la liste complète des utilisateurs.

### V-03 Role Enumeration

Les rôles internes sont exposés :

- admin
- customer
- deluxe
- accounting

### V-04 JWT Exposure

Le JWT est stocké dans LocalStorage et accessible via JavaScript.

### V-05 Broken Object Level Authorization

Un utilisateur authentifié peut consulter les paniers d'autres utilisateurs via :

/rest/basket/{id}

Impact : accès non autorisé aux données métier.