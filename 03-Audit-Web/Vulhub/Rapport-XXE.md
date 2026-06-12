# Rapport XXE – Vulhub PHP XXE

## 1. Contexte

Dans le cadre du projet SecureBank, un environnement Vulhub a été déployé afin d'étudier les vulnérabilités XML External Entity (XXE).

L'objectif de cet audit est d'identifier les mécanismes de traitement XML implémentés dans l'application et d'évaluer les risques liés à une mauvaise configuration des parseurs XML.

---

## 2. Reconnaissance

### Identification du service

Commande utilisée :

```bash
curl -I http://10.0.0.200:8080
```

Résultat :

```http
HTTP/1.1 200 OK
X-Powered-By: PHP/7.0.30
Content-type: text/html; charset=UTF-8
```

Analyse :

* Le service HTTP est accessible.
* La version PHP est divulguée via les en-têtes HTTP.

### Identification technologique

Commande utilisée :

```bash
whatweb http://10.0.0.200:8080
```

Résultat :

```text
PHP 7.0.30
Title[phpinfo()]
```

Analyse :

* L'application expose une page phpinfo().
* Les informations système sont directement accessibles.

### Scan Nmap

Commande utilisée :

```bash
nmap -Pn -sV 10.0.0.200 -p 8080
```

Résultat :

```text
8080/tcp open
PHP cli server 5.5 or later (PHP 7.0.30)
```

Analyse :

* Le serveur HTTP utilisé est le serveur intégré de PHP.
* Le port 8080 est accessible depuis le réseau interne.

---

## 3. Cartographie Applicative

### Arborescence identifiée

```text
www/
├── index.php
├── dom.php
├── SimpleXMLElement.php
└── simplexml_load_string.php
```

### Composants observés

| Fichier                   | Description                           |
| ------------------------- | ------------------------------------- |
| index.php                 | Page d'information PHP                |
| dom.php                   | Parsing XML via DOMDocument           |
| SimpleXMLElement.php      | Parsing XML via SimpleXMLElement      |
| simplexml_load_string.php | Parsing XML via simplexml_load_string |

### Surface d'attaque

Les trois scripts suivants acceptent des données XML contrôlées par l'utilisateur :

```text
/dom.php
/SimpleXMLElement.php
/simplexml_load_string.php
```

Ces composants constituent les principaux points d'entrée pour une attaque XXE.

---

## 4. Analyse Technique

### Endpoint : dom.php

Code observé :

```php
$data = file_get_contents('php://input');

$dom = new DOMDocument();
$dom->loadXML($data);
```

Analyse :

Le contenu XML envoyé par l'utilisateur est directement chargé dans un objet DOMDocument sans validation préalable.

---

### Endpoint : SimpleXMLElement.php

Code observé :

```php
$data = file_get_contents('php://input');

$xml = new SimpleXMLElement($data);
```

Analyse :

Le parseur XML SimpleXMLElement traite directement les données fournies par l'utilisateur.

---

### Endpoint : simplexml_load_string.php

Code observé :

```php
$data = file_get_contents('php://input');

$xml = simplexml_load_string($data);

echo $xml->name;
```

Analyse :

Les données XML sont analysées via simplexml_load_string() sans mécanisme de protection visible.

---

### Risque identifié

Les trois endpoints récupèrent directement les données utilisateur via :

```php
file_get_contents('php://input')
```

Puis les transmettent immédiatement à un parseur XML.

Aucun mécanisme de protection contre les entités externes XML n'est visible dans le code analysé.

Cette situation est caractéristique des vulnérabilités XML External Entity (XXE).

---

## 5. Impact

Une vulnérabilité XXE peut permettre à un attaquant :

* d'accéder à des fichiers présents sur le serveur ;
* d'obtenir des informations sensibles ;
* d'interagir avec certains services internes ;
* de provoquer des comportements inattendus du parseur XML ;
* d'augmenter la surface d'attaque de l'application.

### Évaluation du risque

| Critère                  | Évaluation |
| ------------------------ | ---------- |
| Exploitation à distance  | Oui        |
| Authentification requise | Non        |
| Interaction utilisateur  | Non        |
| Impact confidentialité   | Élevé      |
| Impact intégrité         | Moyen      |
| Impact disponibilité     | Moyen      |

### Criticité

**Élevée**

---

## 6. Recommandations

### Correctifs

* Désactiver les entités externes XML lorsque cela est possible.
* Utiliser des parseurs XML configurés en mode sécurisé.
* Mettre à jour les bibliothèques XML utilisées.

### Durcissement

* Valider strictement les documents XML reçus.
* Limiter les fonctionnalités XML non nécessaires.
* Filtrer les entrées utilisateur avant traitement.

### Supervision

* Journaliser les erreurs liées au parsing XML.
* Détecter les requêtes XML anormales.
* Mettre en place une surveillance des endpoints XML.

---

## 7. Conclusion

L'analyse du laboratoire Vulhub PHP XXE a permis d'identifier plusieurs composants réalisant un traitement XML à partir de données entièrement contrôlées par l'utilisateur.

Trois parseurs XML différents sont utilisés :

* DOMDocument
* SimpleXMLElement
* simplexml_load_string

L'absence de mécanismes de protection visibles contre les entités XML externes expose potentiellement l'application à des vulnérabilités de type XXE.

Une sécurisation du traitement XML est recommandée afin de réduire les risques de divulgation d'informations et d'accès non autorisé aux ressources du système.
