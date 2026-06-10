# Charte de Mission

## Informations générales

**Client :** SecureBank

**Prestataire :** SecureOps Consulting

**Type de mission :** Audit de sécurité applicatif et analyse des risques

**Durée de la mission :** 5 jours

**Mode d'audit :** Boîte grise (Grey Box Assessment)

**Référentiels de référence :**

* OWASP Web Security Testing Guide (WSTG)
* OWASP Top 10
* PTES (Penetration Testing Execution Standard)
* PCI-DSS (approche pré-conformité)


# Contexte

SecureBank est une fintech de taille moyenne spécialisée dans les services financiers numériques et les paiements en ligne.

Dans le cadre de sa démarche de renforcement de la sécurité et de préparation à une future mise en conformité PCI-DSS, la direction de SecureBank mandate SecureOps Consulting afin de réaliser un audit de sécurité de son environnement applicatif.

L'objectif est d'identifier les vulnérabilités susceptibles d'affecter la confidentialité, l'intégrité ou la disponibilité des données et services exposés.


# Objectifs de la mission

## Objectif principal

Évaluer le niveau de sécurité des applications du périmètre afin d'identifier les vulnérabilités exploitables et les risques associés.

## Objectifs spécifiques

* Cartographier les services exposés.
* Identifier les vulnérabilités applicatives.
* Évaluer les mécanismes d'authentification.
* Évaluer les mécanismes d'autorisation.
* Vérifier la protection des données sensibles.
* Mesurer les impacts métier potentiels.
* Produire des recommandations de remédiation.


# Périmètre de l'audit

## Applications incluses

| Titre | Description |
| --- | --- |
| OWASP Juice Shop | Application e-commerce volontairement vulnérable servant de cible principale de l'audit. |
| Vulhub SSRF | Évaluation des risques liés aux attaques Server-Side Request Forgery. |
| Vulhub XXE | Évaluation des risques liés au traitement non sécurisé des données XML. |
| Vulhub Auth Bypass | Évaluation des mécanismes de contrôle d'accès et d'authentification. |
| Hack-Oeil | Plateforme d'entraînement utilisée lors de la phase préparatoire de l'équipe. |


# Hors périmètre

Les éléments suivants ne font pas partie de la mission :

* Hyperviseur VirtualBox
* Système d'exploitation hôte
* Infrastructure réseau physique
* Équipements tiers
* Services externes non explicitement inclus dans le scénario


# Hypothèses de travail

La mission est réalisée selon une approche boîte grise.

L'équipe d'audit dispose :

* d'un accès légitime à l'environnement ;
* d'informations générales sur l'architecture ;
* d'autorisations explicites de test sur l'ensemble du périmètre.


# Règles d'engagement

Les activités suivantes sont autorisées :

* Reconnaissance et cartographie
* Analyse applicative
* Validation des vulnérabilités
* Tests d'authentification
* Tests d'autorisation
* Vérification des mécanismes de sécurité

Les activités suivantes sont interdites :

* Déni de service volontaire
* Destruction de données
* Modification permanente des systèmes
* Sortie du périmètre défini


# Livrables attendus

À l'issue de la mission, SecureOps Consulting remettra :

* Un rapport d'audit technique détaillé
* Un Executive Summary destiné à la Direction Générale
* Une matrice de criticité des risques
* Une feuille de route de remédiation priorisée
* Un dossier de preuves techniques


# Organisation de l'équipe

## Audit Lead

Coordination de la mission, suivi des travaux, consolidation des résultats et validation des livrables.

## Analyste Sécurité Web

Audit des applications web, validation des vulnérabilités applicatives et production des preuves techniques.

## Analyste Sécurité Infrastructure

Reconnaissance, cartographie des services, analyse de la surface d'attaque et support aux investigations techniques.


# Critères de réussite

La mission sera considérée comme réussie si :

* Les actifs du périmètre sont identifiés.
* Les vulnérabilités majeures sont documentées.
* Les risques sont évalués selon leur criticité.
* Les recommandations sont priorisées.
* Les livrables sont exploitables par les équipes techniques et la direction.
