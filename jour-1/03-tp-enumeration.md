# TP 3 — Énumération des services

**Phase CEH :** 2. Scan (énumération) · **Durée :** ~1 h 15 · **Rendu individuel**

## Objectifs

- Faire « parler » les services ouverts repérés au TP 2.
- Extraire comptes, partages et configurations.
- Relier chaque fuite d'information à une mesure de durcissement.

## Périmètre

Cible **unique** : **Metasploitable 2** dans le réseau `lab-ceh`. Partir du tableau ports/services du
TP 2. Remplacer `<CIBLE>` par l'IP de la cible.

---

## Partie A — SMB / NetBIOS

```bash
# Vue d'ensemble : partages, utilisateurs, groupes
enum4linux -a <CIBLE>

# Lister les partages
smbclient -L //<CIBLE>/ -N

# Se connecter à un partage ouvert
smbclient //<CIBLE>/tmp -N
```

Relever : noms d'utilisateurs, partages accessibles, niveau d'authentification requis.

## Partie B — SNMP

> ⚠️ **Prérequis.** Sur un Metasploitable 2 **non modifié**, SNMP n'écoute que sur `localhost` : le
> `snmpwalk` depuis Kali ne renvoie rien. Il faut l'ouvrir **une fois** sur la cible (idéalement déjà
> fait dans le snapshot « corrigé » par l'enseignant) :
>
> ```bash
> # Sur Metasploitable (login msfadmin / msfadmin) :
> sudo sed -i 's/127.0.0.1/0.0.0.0/' /etc/default/snmpd
> sudo /etc/init.d/snmpd restart
> ```
>
> Vérifier ensuite depuis Kali que le port est ouvert : `nmap -sU -p 161 <CIBLE>` → `161/udp open snmp`.

```bash
# Parcourir l'arbre SNMP avec la communauté par défaut
snmpwalk -v1 -c public <CIBLE>
```

Que révèle une communauté restée sur `public` ? (processus, interfaces, parfois comptes)

## Partie C — NFS, SMTP, DNS

```bash
# Partages NFS exportés
showmount -e <CIBLE>

# Utilisateurs SMTP (vérification d'existence)
nmap -p25 --script smtp-enum-users <CIBLE>

# Tentative de transfert de zone DNS (si un DNS est exposé)
dig axfr @<CIBLE> <domaine-du-lab>
```

## Partie D — Contre-mesures

Pour **chaque** service énuméré, proposer un durcissement concret :

| Service | Information fuitée | Durcissement |
| --- | --- | --- |
| SMB | comptes, partages | désactiver le listing anonyme, restreindre par ACL |
| SNMP | config complète | changer la communauté, passer en SNMPv3, filtrer |
| NFS | exports | limiter aux hôtes autorisés, `root_squash` |
| SMTP | comptes valides | désactiver VRFY/EXPN |
| DNS | zone complète | restreindre les transferts de zone aux secondaires |

---

## Livrable

1. **Liste des comptes et partages** découverts sur la cible.
2. Pour chacun, **une** recommandation de durcissement (tableau Partie D).

À reporter dans [`grille-compte-rendu.md`](grille-compte-rendu.md).

## Synthèse du Jour 1

Vous avez enchaîné les trois premières étapes d'une mission : **observer** (reconnaissance),
**cartographier** (scan), **détailler** (énumération). Message clé : *un service inutile exposé est
déjà une faille.* La défense commence par réduire la surface d'attaque.
