# Rapport d'audit — GeoServer CVE-2024-36401

## 1. Contexte

Dans le cadre de l'audit de sécurité du projet SecureBank, un environnement Vulhub basé sur GeoServer a été déployé afin d'étudier une vulnérabilité critique affectant certaines versions de GeoServer.

La vulnérabilité analysée est référencée sous l'identifiant CVE-2024-36401.

---

## 2. Présentation de la vulnérabilité

### Produit concerné

GeoServer

### Version vulnérable observée

GeoServer 2.23.2

### Type de vulnérabilité

Remote Code Execution non authentifiée

### Composant affecté

Traitement des expressions de noms de propriétés dans certaines requêtes OGC.

### Description

La vulnérabilité provient d'une évaluation non sécurisée de certains paramètres de requêtes OGC. Un utilisateur non authentifié peut envoyer une requête spécialement construite afin de déclencher l'évaluation d'expressions dangereuses côté serveur.

---

## 3. Environnement de test

| Élément | Valeur |
|----------|---------|
| Plateforme | Vulhub |
| Application | GeoServer |
| Version | 2.23.2 |
| Adresse IP | 10.0.0.200 |
| Port | 8080 |
| Serveur HTTP | Jetty 9.4.51.v20230217 |

---

## 4. Reconnaissance

### Nmap

Commande utilisée :

```bash
nmap -sV 10.0.0.200 -p 8080