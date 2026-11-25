# Jour 1 — Fondamentaux & reconnaissance

Première journée (7 h, 9h–17h). Objectif : savoir dresser la **surface d'attaque** d'une cible —
informations publiques, hôtes vivants, ports, services et comptes — dans un laboratoire isolé.

## Avant la séance

1. Installer le laboratoire : [`00-guide-installation.md`](00-guide-installation.md).
2. Vérifier que Kali voit la cible (voir le guide, §6).

## Déroulé

| Créneau | Activité |
| --- | --- |
| 9h00–10h15 | Cours : cadre légal, 5 phases, mise en route du lab |
| 10h15–11h00 | Vérification des installations + cadre légal (à l'oral) |
| 11h00–12h30 | **TP 1 — Reconnaissance / OSINT** |
| 13h30–15h30 | **TP 2 — Scan de réseau** (Nmap & Wireshark) |
| 15h30–17h00 | **TP 3 — Énumération** |

> Les horaires des TP sont indicatifs : l'essentiel est d'enchaîner cours → TP correspondant.

## Fichiers

| Fichier | Contenu |
| --- | --- |
| [`00-guide-installation.md`](00-guide-installation.md) | Montage du lab (VMware & VirtualBox, réseau NAT) |
| [`01-tp-reconnaissance-osint.md`](01-tp-reconnaissance-osint.md) | TP 1 — footprinting et OSINT |
| [`02-tp-scan-reseau.md`](02-tp-scan-reseau.md) | TP 2 — scan Nmap + Wireshark |
| [`03-tp-enumeration.md`](03-tp-enumeration.md) | TP 3 — énumération des services |
| [`grille-compte-rendu.md`](grille-compte-rendu.md) | Modèle de compte rendu + barème |

## Prérequis étudiants (validés en début de séance)

- [ ] Ouvrir un terminal Linux et naviguer (`cd`, `ls`, `cat`, `man`)
- [ ] Notions de port, service, adresse IP
- [ ] Différence passif / actif en reconnaissance
- [ ] Laboratoire installé et **réseau vérifié** (`ping` vers la cible OK)
