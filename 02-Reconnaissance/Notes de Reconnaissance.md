# Notes de Reconnaissance

## Résumé exécutif

La phase de reconnaissance a permis d'identifier avec succès l'application cible OWASP Juice Shop hébergée sur le port TCP 3000.

---

## Découvertes principales

### Technologies

* Angular
* Node.js
* Express
* API REST

### Ressources

```text
/assets/
/media/
/ftp
/promotion
/robots.txt
/video
```

### Routes découvertes

```text
/#/jobs
```

### Headers intéressants

```http
Access-Control-Allow-Origin: *
X-Recruiting: /#/jobs
```

---

## Vulnérabilités à investiguer

### Priorité Haute

* SQL Injection
* XSS
* IDOR
* Broken Access Control
* Authentication Issues
* JWT

### Priorité Moyenne

* CORS
* Information Disclosure

---

## Phase suivante

03-Vulnerabilites/

* Cartographie API
* Analyse JWT
* SQL Injection
* XSS
* IDOR
* Broken Access Control
