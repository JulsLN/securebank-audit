# Inventaire des Services

## Service principal

| Port | Protocole | État |
| ---- | --------- | ---- |
| 3000 | TCP       | Open |

---

## Application identifiée

```html
<title>OWASP Juice Shop</title>
```

---

## Technologies détectées

### WhatWeb

```bash
whatweb http://10.0.0.200:3000
```

Résultat :

* HTML5
* Angular (probable)
* Node.js (probable)
* Express (probable)

---

## En-têtes HTTP observés

### Sécurité

```http
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Feature-Policy: payment 'self'
```

### CORS

```http
Access-Control-Allow-Origin: *
```

### Header personnalisé

```http
X-Recruiting: /#/jobs
```

---

## Méthodes HTTP observées

```http
GET
HEAD
POST
PUT
PATCH
DELETE
```

---

## Conclusion

L'application repose sur une architecture SPA Angular consommant une API REST exposée par un backend Node.js/Express.
