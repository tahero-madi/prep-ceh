# TP 2 — Scan de réseau (Nmap & Wireshark)

**Phase CEH :** 2. Scan · **Durée :** ~1 h 15 · **Rendu individuel**

## Objectifs

- Découvrir les hôtes vivants et les ports ouverts d'une cible.
- Comprendre les grands types de scans et **leur signature réseau**.
- Identifier services et versions pour préparer l'énumération.

## Périmètre

Cible **unique** : la VM **Metasploitable 2**, dans le réseau `lab-ceh`. Vérifier d'abord que Kali
voit la cible (voir le guide d'installation, §6). Remplacer `<CIBLE>` par l'IP de Metasploitable.

---

## Partie A — Découverte d'hôtes

```bash
# Quelles machines répondent sur le segment ?
sudo nmap -sn 192.168.56.0/24
```

Noter l'IP de la cible. Rôle de chaque hôte détecté ?

## Partie B — Les types de scan

Lancer chaque scan et comparer les résultats :

```bash
# Scan SYN (demi-ouvert), rapide et discret
sudo nmap -sS <CIBLE>

# Scan TCP connect (établit la connexion complète)
nmap -sT <CIBLE>

# Scan UDP (plus lent — cibler quelques ports)
sudo nmap -sU --top-ports 20 <CIBLE>

# Détection de versions et d'OS
sudo nmap -sV -O <CIBLE>

# Scan complet « de référence » pour le rapport
sudo nmap -sS -sV -p- <CIBLE> -oN scan-metasploitable.txt
```

Pour chaque scan, relever : nombre de ports ouverts, durée, bruit généré.

## Partie C — Observer le scan dans Wireshark

1. Lancer **Wireshark** sur `eth0`, filtre d'affichage : `ip.addr == <CIBLE>`.
2. Relancer un **SYN scan** (`-sS`) et observer : paquets **SYN** envoyés, réponses **SYN/ACK** (port
   ouvert) ou **RST** (port fermé).
3. Relancer un **TCP connect** (`-sT`) et observer la poignée de main complète (SYN → SYN/ACK → ACK).
4. Comparer la signature des deux scans.

> 💡 C'est cette différence de signature qu'un IDS exploite pour **détecter** un scan. On y reviendra
> côté défense au Jour 4.

## Partie D — Contre-mesures

- Quels ports ouverts sont **inutiles** et devraient être fermés ?
- Comment un pare-feu et un IDS modifieraient-ils les résultats d'un scan ?

---

## Livrable

Un **tableau ports / services / versions** de la cible :

| Port | Protocole | Service | Version | Utile ? | Action recommandée |
| --- | --- | --- | --- | --- | --- |
| 21 | tcp | ftp | vsftpd 2.3.4 | … | … |
| … | | | | | |

À reporter dans [`grille-compte-rendu.md`](grille-compte-rendu.md), avec le fichier
`scan-metasploitable.txt`.

## Attention

- Un scan `-p-` + `-sV` peut être long : lancer, puis enchaîner sur Wireshark pendant qu'il tourne.
- Rester **sur la seule cible autorisée** (Metasploitable). Aucun scan vers une autre machine.
