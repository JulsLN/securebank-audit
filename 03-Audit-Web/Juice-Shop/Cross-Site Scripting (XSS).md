## 2. Cross-Site Scripting (XSS)

### Points d'entrée identifiés

- Recherche produit
- Feedback

### Indicateurs observés

- Affichage de contenu utilisateur dans l'interface

### Risque

Injection de code JavaScript exécuté dans le navigateur de la victime.

Conséquences possibles :

- Vol de session
- Vol de JWT
- Défiguration de l'interface