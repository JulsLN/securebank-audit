# Classification CVSS – SecureBank

## Méthodologie

Les vulnérabilités ont été classées selon une estimation basée sur le standard CVSS v3.1.

---

# Vuln-001 — SQL Injection Authentication Bypass

### Score estimé

```text
CVSS 9.8
```

### Niveau

```text
CRITIQUE
```

### Justification

* Exploitable à distance
* Aucune authentification requise
* Impact total sur la confidentialité
* Impact total sur l'intégrité

---

# Vuln-002 — DOM-Based XSS

### Score estimé

```text
CVSS 7.4
```

### Niveau

```text
ÉLEVÉ
```

### Justification

* Exécution de JavaScript arbitraire
* Vol potentiel de session
* Nécessite une interaction utilisateur

---

# Vuln-004 — JWT Information Disclosure

### Score estimé

```text
CVSS 5.5
```

### Niveau

```text
MOYEN
```

### Justification

* Divulgation d'informations sensibles
* Pas de compromission directe
* Facilite d'autres attaques

---

# Vuln-005 — Weak Authentication Controls

### Score estimé

```text
CVSS 5.3
```

### Niveau

```text
MOYEN
```

### Justification

* Facilite les attaques automatisées
* Nécessite des tentatives répétées
* Dépend de la robustesse des mots de passe

---

# Synthèse

| Vulnérabilité       | Score CVSS | Niveau   |
| ------------------- | ---------- | -------- |
| SQL Injection       | 9.8        | Critique |
| DOM XSS             | 7.4        | Élevé    |
| JWT Disclosure      | 5.5        | Moyen    |
| Weak Authentication | 5.3        | Moyen    |

---

# Conclusion

Une vulnérabilité critique a été identifiée.

La correction de la SQL Injection constitue la priorité absolue.
