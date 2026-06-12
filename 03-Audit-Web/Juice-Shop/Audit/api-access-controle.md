## Contrôles d'accès cassés sur les objets API

Nous avons déjà trouvé :
``` GET /api/Users ```
et
``` GET /rest/basket/{id} ```
qui exposent des données.
Maintenant, nous allons voir si nous pouvons accéder à d'autres objets métier.

Depuis Kali, avec le JWT valide, faite des curl sur :

```
curl -s -H "Authorization: Bearer JWT" \
http://10.10.10.2:3000/api/<ENPOINT>
```

### 1. /api/Feedbacks

Réponse :
```
{
  "UserId":21,
  "id":4,
  "comment":"Please send me the juicy chatbot NFT..."
}
```

Un simple utilisateur authentifié récupère :

* tous les feedbacks ;
* les identifiants internes (UserId) ;
* le contenu complet des commentaires.
Constat

Le système ne limite pas l'accès aux seules ressources de l'utilisateur connecté.

### Risque
* collecte d'informations métier ;
* corrélation utilisateurs ↔ contenus ;
* préparation d'attaques ciblées.

OWASP API
``` API3: Excessive Data Exposure ```

et potentiellement

``` API1: Broken Object Level Authorization ```


### 2. /api/Complaints

Réponse :
```
{
  "UserId":3,
  "message":"I'll build my own eCommerce business!"
}
```

Un client récupère les réclamations d'autres utilisateurs.

* Vulnérabilité :
Un objet appartenant à l'utilisateur 3 est visible par l'utilisateur 24.

* Classification :
  Broken Object Level Authorization (BOLA)

* Gravité :
  Élevée

### 3. /api/Challenges

Plus intéressant, on récupères :

```
{
 "name":"Admin Registration",
 "description":"Register as a user with administrator privileges."
}
```

ainsi que la totalité des challenges.

### Problème 1 : divulgation fonctionnelle

L'application révèle :

* les fonctionnalités cachées ;
* les mécanismes vulnérables ;
* les points d'entrée potentiels ;
* certaines dépendances.

Exemple :
```
{
 "key":"jwtUnsignedChallenge"
}
{
 "key":"registerAdminChallenge"
}
{
 "key":"ssrfChallenge"
}
{
 "key":"lfrChallenge"
}
```

### Problème 2 : fuite d'informations sensibles

On récupéré :
```
{
 "key":"ALCHEMY_API_KEY",
 "missing":true
}
```

et :
```
{
 "key":"http://localhost:11434/v1"
}
```

Ces informations n'ont aucune raison d'être accessibles à un simple utilisateur.