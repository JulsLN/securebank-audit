# Cartographie Réseau

## Objectif

Identifier les hôtes accessibles et les services exposés sur la cible.

---

## Cible

| Élément        | Valeur           |
| -------------- | ---------------- |
| Adresse IP     | 10.0.0.200       |
| Application    | OWASP Juice Shop |
| Port principal | 3000/TCP         |

---

## Découverte de l'hôte

### Ping

```bash
ping -c 4 10.0.0.200
```

### Résultat

```text
Host reachable
Latency < 1 ms
```

---

## Scan Nmap

### Commande

```bash
nmap -sV -sC -p3000 10.0.0.200
```

### Résultat

```text
3000/tcp open
```

### Observation

La cible expose un unique service web accessible sur le port TCP 3000.

---

## Architecture observée

```text
[Kali Linux]
      │
      ▼
10.0.0.200:3000
      │
      ▼
OWASP Juice Shop
      │
      ├── Angular Frontend
      ├── Node.js
      ├── Express
      └── API REST
```

---

## Conclusion

La surface réseau est volontairement réduite à un seul service HTTP exposé sur le port 3000/TCP.
