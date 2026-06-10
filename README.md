# SecureBank Audit

## Présentation

Dans le cadre d'une mission simulée d'audit de sécurité, la société fictive **SecureBank** a mandaté **SecureOps Consulting** afin d'évaluer le niveau de sécurité de son infrastructure applicative avant une démarche de mise en conformité PCI-DSS.

L'objectif de cette mission est d'identifier les vulnérabilités pouvant affecter la confidentialité, l'intégrité et la disponibilité des systèmes audités, d'évaluer les risques associés et de proposer un plan de remédiation adapté.

---

## Objectifs de la mission

* Réaliser une cartographie de l'infrastructure auditée.
* Identifier les vulnérabilités applicatives et les défauts de configuration.
* Évaluer les mécanismes d'authentification et d'autorisation.
* Mesurer l'impact métier des vulnérabilités identifiées.
* Produire des livrables professionnels destinés à la direction et aux équipes techniques.
* Élaborer une feuille de route de remédiation priorisée.

---

## Périmètre de l'audit

### Applications auditées

* OWASP Juice Shop
* Environnements Vulhub sélectionnés dans le cadre du projet
* Plateformes d'entraînement Hack-Oeil fournies par le formateur

### Hors périmètre

* Hyperviseur VirtualBox
* Système hôte
* Équipements réseau externes
* Services tiers non inclus dans le scénario

---

## Méthodologie

La mission suit une démarche inspirée des bonnes pratiques :

* OWASP Web Security Testing Guide (WSTG)
* PTES (Penetration Testing Execution Standard)
* PCI-DSS
* OWASP Top 10

Les travaux sont organisés autour des phases suivantes :

1. Cadrage de la mission
2. Reconnaissance et cartographie
3. Audit de sécurité applicatif
4. Analyse des risques
5. Production des livrables
6. Recommandations et remédiation

---

## Équipe projet

### SecureOps Consulting

* Audit Lead
* Analyste Sécurité Web
* Analyste Sécurité Infrastructure

---

## Livrables attendus

* Rapport d'audit technique
* Executive Summary
* Matrice des risques
* Plan de remédiation priorisé
* Dossier de preuves techniques

---

## Structure du dépôt

```text
SecureBank-Audit/
├── 01-Cadrage/
├── 02-Reconnaissance/
├── 03-Audit-Web/
├── 04-Analyse-Risques/
├── 05-Livrables/
├── preuves/
├── captures/
├── scans/
└── scripts/
```

---

## Statut du projet

Mission en cours.
