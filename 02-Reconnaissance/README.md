# Phase 1 — Reconnaissance
## Audit de sécurité OWASP Juice Shop

### Objectif

La phase de reconnaissance vise à collecter un maximum d'informations sur l'application cible avant toute phase de test d'intrusion ou d'exploitation.

L'objectif est d'identifier :

- La technologie utilisée
- L'architecture de l'application
- Les points d'entrée accessibles
- Les mécanismes d'authentification
- Les API exposées
- Les fonctionnalités sensibles
- Les potentielles surfaces d'attaque

---

# 1. Identification de la cible

## URL cible

http://10.0.0.100:3000


# Types de vulnérabilités potentiellement présentes

Lors de la phase de reconnaissance, plusieurs zones de risque ont été identifiées.

---

## 1. Injection SQL (SQL Injection)

### Points d'entrée identifiés

- Formulaire de connexion
- Barre de recherche
- Paramètres API
- Formulaires de feedback

### Indicateurs observés

- Nombreuses requêtes API vers la base de données
- Paramètres utilisateur transmis au backend
- Présence d'un système d'authentification

### Risque

Un attaquant pourrait manipuler les requêtes SQL afin de :

- Lire des données sensibles
- Contourner l'authentification
- Modifier ou supprimer des données

---

## 2. Cross-Site Scripting (XSS)

### Points d'entrée identifiés

- Recherche produit
- Avis clients
- Feedback
- Réclamations

### Indicateurs observés

- Affichage de contenu utilisateur dans l'interface
- Application monopage Angular

### Risque

Injection de code JavaScript exécuté dans le navigateur de la victime.

Conséquences possibles :

- Vol de session
- Vol de JWT
- Défiguration de l'interface

---

## 3. Contrôle d'accès défaillant (Broken Access Control)

### Points d'entrée identifiés

- Routes administrateur
- Endpoints API utilisateurs
- Historique des commandes
- Gestion du panier

### Indicateurs observés

- Multiples appels API contenant des identifiants numériques
- Routes non visibles mais accessibles

### Risque

Accès à des ressources appartenant à d'autres utilisateurs.

---

## 4. IDOR (Insecure Direct Object Reference)

### Points d'entrée identifiés

Exemples de ressources :

```text
/api/Users/
/api/Orders/
/api/BasketItems/
/api/Addresss/