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

> 💡 **SNMP ?** On n'énumère pas SNMP aujourd'hui : il est **désactivé par défaut** sur Metasploitable 2
> (il n'écoute que sur `localhost`) et on ne modifie pas la cible. SNMP est abordé en **théorie** dans le
> cours ; on retient surtout qu'une communauté restée sur `public` livrerait toute la configuration.

## Partie B — NFS, SMTP, DNS

```bash
# Partages NFS exportés
showmount -e <CIBLE>

# Utilisateurs SMTP (vérification d'existence)
nmap -p25 --script smtp-enum-users <CIBLE>

# Tentative de transfert de zone DNS (si un DNS est exposé)
dig axfr @<CIBLE> <domaine-du-lab>
```

## Partie C — Contre-mesures

Pour **chaque** service énuméré, proposer un durcissement concret :

| Service | Information fuitée | Durcissement |
| --- | --- | --- |
| SMB | comptes, partages | désactiver le listing anonyme, restreindre par ACL |
| NFS | exports | limiter aux hôtes autorisés, `root_squash` |
| SMTP | comptes valides | désactiver VRFY/EXPN |
| DNS | zone complète | restreindre les transferts de zone aux secondaires |

---

## Livrable

1. **Liste des comptes et partages** découverts sur la cible.
2. Pour chacun, **une** recommandation de durcissement (tableau Partie C).

À reporter dans [`grille-compte-rendu.md`](grille-compte-rendu.md).

## Synthèse du Jour 1

Vous avez enchaîné les trois premières étapes d'une mission : **observer** (reconnaissance),
**cartographier** (scan), **détailler** (énumération). Message clé : *un service inutile exposé est
déjà une faille.* La défense commence par réduire la surface d'attaque.
