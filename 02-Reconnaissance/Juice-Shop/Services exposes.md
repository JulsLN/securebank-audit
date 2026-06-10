# Services exposés

## Objectif

L'objectif de cette étape est d'identifier les services accessibles depuis le réseau afin de déterminer la surface d'attaque de l'application.

---

## Méthodologie

Les services exposés ont été identifiés à l'aide de l'outil Nmap.

### Commande utilisée

```bash
nmap -sV -sC -p3000 10.0.0.200


### Résultat 
Ports ouverts

| Port | Protocole | État | Service                          |
| ---- | --------- | ---- | -------------------------------- |
| 3000 | TCP       | Open | Application Web OWASP Juice Shop |

