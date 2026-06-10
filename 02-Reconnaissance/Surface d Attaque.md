# Surface d'Attaque

## Objectif

Identifier tous les points d'entrée accessibles depuis l'extérieur.

---

## Surface Web

### Application principale

```text
http://10.0.0.200:3000
```

---

## Ressources découvertes

### Gobuster

```bash
gobuster dir -u http://10.0.0.200:3000 \
-w /usr/share/wordlists/dirb/common.txt \
--exclude-length 9903
```

### Résultats

```text
/assets/
/media/
/ftp
/promotion
/robots.txt
/video
```

---

## Ressources sensibles

### robots.txt

```text
User-agent: *
Disallow: /ftp
```

### Route cachée

```text
/#/jobs
```

---

## Endpoints potentiels

Réponses HTTP 500 observées :

```text
/api
/apis
/profile
/rest
/redirect
/restaurants
/restricted
```

---

## Vulnérabilités potentielles

* SQL Injection
* XSS
* IDOR
* Broken Access Control
* JWT Weaknesses
* Information Disclosure
* CORS Misconfiguration

---

## Conclusion

La surface d'attaque est principalement constituée des fonctionnalités web et des API REST exposées par l'application.
