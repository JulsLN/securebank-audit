# Priorisation des Risques – SecureBank

## Objectif

Déterminer l'ordre de traitement des vulnérabilités identifiées.

---

# Priorité P1

## Vuln-001 — SQL Injection Authentication Bypass

### Criticité

Critique

### Délai recommandé

```text
0 à 7 jours
```

### Justification

Permet une compromission complète de l'application.

---

# Priorité P2

## Vuln-002 — DOM-Based XSS

### Criticité

Élevée

### Délai recommandé

```text
7 à 30 jours
```

### Justification

Permet le vol de session et l'exécution de code côté client.

---

# Priorité P3

## Vuln-004 — JWT Information Disclosure

### Criticité

Moyenne

### Délai recommandé

```text
30 à 60 jours
```

### Justification

Divulgation d'informations sensibles pouvant faciliter d'autres attaques.

---

# Priorité P4

## Vuln-005 — Weak Authentication Controls

### Criticité

Moyenne

### Délai recommandé

```text
30 à 60 jours
```

### Justification

Augmente le risque d'attaques automatisées contre les comptes utilisateurs.

---

# Feuille de route

| Priorité | Vulnérabilité       | Échéance |
| -------- | ------------------- | -------- |
| P1       | SQL Injection       | 7 jours  |
| P2       | XSS                 | 30 jours |
| P3       | JWT Disclosure      | 60 jours |
| P4       | Weak Authentication | 60 jours |

---

# Conclusion

La correction immédiate de la vulnérabilité SQL Injection est indispensable.

Les vulnérabilités XSS, JWT et Weak Authentication doivent ensuite être corrigées afin de réduire durablement la surface d'attaque et d'améliorer la posture de sécurité globale de SecureBank.
