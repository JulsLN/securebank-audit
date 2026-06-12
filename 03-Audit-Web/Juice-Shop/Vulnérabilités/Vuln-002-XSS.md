# Vuln-002 — Cross-Site Scripting (XSS) dans la recherche

## Gravité

Élevée

## Endpoint concerné

GET /#/search?q=<payload>

Fonctionnalité :
Recherche de produits

## Description

La valeur du paramètre de recherche est réinjectée dans le DOM sans neutralisation suffisante.

Un attaquant peut exécuter du JavaScript arbitraire dans le navigateur d'un utilisateur.

## Reproduction

### Payload 1

<img src=x onerror=alert(1)>

### Résultat

Exécution d'une fenêtre JavaScript :

alert(1)

### Payload 2

<iframe src="javascript:alert(1)">

### Résultat

Exécution de JavaScript dans le contexte de l'application.

## Impact

Un attaquant peut :

- voler des sessions utilisateur
- récupérer des JWT
- effectuer des actions au nom d'un utilisateur connecté
- modifier l'affichage de l'application
- rediriger l'utilisateur vers un site malveillant

## Preuves

- xss-search-img-onerror.png
- xss-search-iframe.png

## Criticité

CVSS estimé : 6.1 à 8.0

## Recommandations

- Encodage systématique des sorties HTML
- Utilisation de textContent plutôt que innerHTML
- Politique CSP restrictive
- Validation et filtrage des entrées utilisateur