# Analyse des Impacts Métier – SecureBank

## Objectif

Évaluer les conséquences métier des vulnérabilités identifiées.

---

# Vuln-001 — SQL Injection

## Impacts

### Confidentialité

Accès aux données utilisateurs et administrateurs.

### Intégrité

Modification potentielle des données.

### Disponibilité

Possibilité de suppression ou corruption des données.

### Impact métier

* Perte de confiance client
* Incident de sécurité majeur
* Non-conformité PCI-DSS

---

# Vuln-002 — DOM XSS

## Impacts

### Confidentialité

Vol de session utilisateur.

### Intégrité

Actions réalisées au nom de la victime.

### Impact métier

* Usurpation d'identité
* Compromission de comptes clients
* Dégradation de l'image de marque

---

# Vuln-004 — JWT Disclosure

## Impacts

### Confidentialité

Exposition d'informations internes.

### Impact métier

* Facilitation des attaques ciblées
* Collecte d'informations pour un attaquant

---

# Vuln-005 — Weak Authentication Controls

## Impacts

### Confidentialité

Augmentation du risque de compromission de comptes.

### Impact métier

* Prise de contrôle de comptes utilisateurs
* Multiplication des tentatives de connexion frauduleuses

---

# Synthèse métier

| Vulnérabilité       | Impact métier |
| ------------------- | ------------- |
| SQL Injection       | Très fort     |
| XSS                 | Fort          |
| JWT Disclosure      | Moyen         |
| Weak Authentication | Moyen         |

---

# Conclusion

La compromission d'un compte administrateur via SQL Injection représente le scénario de risque métier le plus critique identifié durant la mission.
