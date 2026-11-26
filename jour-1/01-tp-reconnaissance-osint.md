# TP 1 — Reconnaissance (footprinting & OSINT)

**Phase CEH :** 1. Reconnaissance · **Durée :** ~1 h 15 · **Rendu individuel**

## Objectifs

- Distinguer reconnaissance **passive** et **active**.
- Collecter, sans toucher la cible, les informations qui préparent une attaque.
- Produire une **cartographie de la surface d'attaque**.

## Cible et périmètre

- **Chaque étudiant choisit une organisation réelle** : une grande entreprise, une université, une
  marque connue, un média… Si vous hésitez, demandez une suggestion à l'enseignant.
- **Reconnaissance passive uniquement** : on lit des informations *publiques* (WHOIS, DNS, certificats,
  pages web, réseaux sociaux). On **ne scanne pas**, on **ne se connecte pas**, on ne contacte pas la cible.
- Le scan et l'énumération **actifs** se font au **TP 2 et au TP 3, sur Metasploitable, dans le labo** —
  jamais sur la cible du TP 1.
- Les outils fonctionnent directement (VM en réseau NAT, accès Internet).

> Dans les commandes, remplacez `<domaine>` par le domaine de la cible que vous avez choisie.

## Partie A — Reconnaissance passive (sources publiques)

1. **WHOIS** — propriétaire, dates, contacts techniques.
   ```bash
   whois <domaine>
   ```
2. **DNS** — enregistrements A, MX, NS, TXT.
   ```bash
   host -t A <domaine>
   host -t MX <domaine>
   dig <domaine> ANY +noall +answer
   ```
3. **Sous-domaines & certificats** — *Certificate Transparency* via `crt.sh` (dans le navigateur) :
   lister les sous-domaines visibles.
4. **Empreinte humaine** — adresses e-mail, format des identifiants (`prenom.nom@…`), technologies
   mentionnées, profils publics.

## Partie B — Outils de collecte

```bash
# Collecte d'e-mails, sous-domaines et hôtes
theHarvester -d <domaine> -b bing

# Cartographie des relations (démonstration)
maltego
```

Pour chaque outil, notez **quelle information** il apporte et vérifiez que vous restez en **passif**.

> 💡 **Google dorks / Shodan** : à présenter en démonstration. Comprendre le principe (`site:`,
> `filetype:`, `intitle:`) sans lancer d'action intrusive.

## Partie C — Contre-mesures

Pour chaque type d'information trouvée, proposez **une** mesure de réduction d'exposition
(anonymiser le WHOIS, cloisonner les sous-domaines de test, sensibiliser au format des e-mails…).

## Livrable

Une **cartographie de la surface d'attaque** de la cible choisie (1 page) :

- Cible choisie (domaine)
- Domaine et sous-domaines identifiés
- Adresses e-mail et format des identifiants
- Technologies / services devinés
- Plages d'adresses éventuelles
- 3 contre-mesures priorisées

À reporter dans [`grille-compte-rendu.md`](grille-compte-rendu.md).

## Rappel important

La reconnaissance **passive** sur des informations publiques est licite ; le **scan actif**, lui, ne
l'est que sur une cible autorisée (ici, le laboratoire). **En cas de doute, on reste passif.**
