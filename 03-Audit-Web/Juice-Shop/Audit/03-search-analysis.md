## Analyse du moteur de recherche

Endpoint :

``` GET /rest/products/search?q= ```|

### Tests réalisés

| Entrée | Résultat                    |
| ------ | --------------------------- |
| apple  | Produits Apple              |
| '      | Aucun résultat              |
| ''     | Produit spécifique retourné |
| "      | Nombreux résultats          |
| OR 1=1 | Aucun résultat              |
| *      | Aucun résultat              |
| .*     | Aucun résultat              |
| %      | Catalogue quasi complet     |

### Observations

Le moteur de recherche ne semble pas vulnérable aux injections SQL triviales.

Cependant, l'utilisation du caractère `%` permet d'obtenir une liste très large de produits, suggérant l'utilisation d'un mécanisme de recherche basé sur l'opérateur SQL LIKE.

Cette fonctionnalité facilite l'énumération complète du catalogue de produits.

### Impact 

* extraction du catalogue ;
* scraping facilité ;
* cartographie des produits.
