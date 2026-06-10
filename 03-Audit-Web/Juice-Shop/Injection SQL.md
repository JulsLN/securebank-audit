## 1. Injection SQL (SQL Injection)

### Points d'entrée identifiés

- Formulaire de connexion 
- Barre de recherche 
- Formulaires de feedback

### Indicateurs observés

- Des requêtes API sont observer 
- Paramètres utilisateur transmis au backend
- Présence d'un système d'authentification 

### Risque

Un attaquant pourrait manipuler les requêtes SQL afin de :

- Lire des données sensibles
- Contourner l'authentification
- Modifier ou supprimer des données