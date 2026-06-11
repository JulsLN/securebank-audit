# Matrice des Risques – SecureBank

## Méthodologie

L'évaluation des risques repose sur :

* Probabilité d'exploitation
* Impact métier
* Criticité globale

Échelle utilisée :

| Niveau   | Valeur |
| -------- | ------ |
| Faible   | 1      |
| Moyen    | 2      |
| Élevé    | 3      |
| Critique | 4      |

---

# Matrice

| Vulnérabilité                       | Probabilité | Impact | Niveau   |
| ----------------------------------- | ----------- | ------ | -------- |
| SQL Injection Authentication Bypass | 4           | 4      | Critique |
| DOM-Based XSS                       | 3           | 3      | Élevé    |
| JWT Information Disclosure          | 2           | 2      | Moyen    |
| Weak Authentication Controls        | 3           | 2      | Moyen    |

---

# Représentation visuelle

| Impact / Probabilité | Faible | Moyen      | Élevé | Critique |
| -------------------- | ------ | ---------- | ----- | -------- |
| Critique             |        |            |       | SQLi     |
| Élevé                |        |            | XSS   |          |
| Moyen                |        | JWT / Auth |       |          |
| Faible               |        |            |       |          |

---

# Conclusion

La vulnérabilité SQL Injection représente le risque principal identifié durant l'audit.

Elle permet à elle seule une compromission complète de l'application et nécessite une correction immédiate.
