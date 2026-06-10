# Énumération Web

## WhatWeb

### Commande

```bash
whatweb http://10.0.0.200:3000
```

### Résultats

* HTML5
* Headers de sécurité
* Titre : OWASP Juice Shop

---

## Nikto

### Commande

```bash
nikto -h http://10.0.0.200:3000
```

### Résultats

#### CORS

```http
Access-Control-Allow-Origin: *
```

#### Header personnalisé

```http
X-Recruiting: /#/jobs
```

#### robots.txt

```text
Disallow: /ftp
```

---

## Analyse des Headers

### Présents

```http
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
```

### À vérifier ultérieurement

* CSP
* HSTS
* Secure Cookie
* HttpOnly Cookie

---

## Conclusion

L'application présente plusieurs mécanismes de sécurité HTTP mais expose également plusieurs informations utiles à la cartographie applicative.
