# TP 1 — Reconnaissance (footprinting & OSINT)

**Phase CEH :** 1. Reconnaissance · **Durée :** ~1 h 15 · **Binôme**

## Objectifs

- Distinguer reconnaissance **passive** et **active**.
- Collecter, sans toucher directement la cible, les informations qui préparent une attaque.
- Produire une **cartographie de la surface d'attaque**.

## Règle de périmètre

La reconnaissance ne se pratique que sur une cible autorisée. Pour ce TP :

- Travail principal sur le **dossier d'organisation fictive** fourni par l'enseignant (fichiers dans
  `jour-1/ressources/` ou distribués en séance).
- Démonstration d'outils **uniquement** sur des cibles publiques explicitement autorisées à
  l'entraînement : `scanme.nmap.org`, `example.com`. **Jamais** sur un domaine réel non autorisé.
- Les outils nécessitant Internet fonctionnent directement (les VM sont en NAT).

---

## Partie A — Reconnaissance passive (sans contact direct)

À partir du dossier fictif et des sources publiques autorisées, renseigner :

1. **WHOIS** — propriétaire, dates, contacts techniques d'un domaine.
   ```bash
   whois example.com
   ```
2. **DNS** — enregistrements A, MX, NS, TXT.
   ```bash
   host -t A example.com
   host -t MX example.com
   dig example.com ANY +noall +answer
   ```
3. **Sous-domaines & certificats** — notion de *Certificate Transparency* (ex. `crt.sh` dans un
   navigateur). Lister les sous-domaines visibles.
4. **Empreinte humaine** — à partir du dossier fictif : adresses e-mail, format des identifiants
   (`prenom.nom@…`), technologies mentionnées, profils publics.

> 💡 **Shodan / Google dorks** : à présenter en démonstration par l'enseignant. Comprendre le principe
> (`site:`, `filetype:`, `intitle:`) sans cibler d'organisation réelle.

## Partie B — Outils de collecte

Sur une cible autorisée (`example.com`) ou le dossier fictif :

```bash
# Collecte d'e-mails, sous-domaines, hôtes
theHarvester -d example.com -b bing

# Cartographie des relations (lancer l'interface, explorer un transform)
maltego   # optionnel, en démonstration
```

Noter pour chaque outil : **quelle information** il apporte et **si elle est passive ou active**.

## Partie C — Contre-mesures

Pour chaque type d'information trouvée, proposer **une** mesure de réduction d'exposition
(ex. : limiter les données WHOIS, cloisonner les sous-domaines de test, sensibiliser au format des
e-mails).

---

## Livrable

Une **cartographie de la surface d'attaque** (1 page) contenant :

- Domaine(s) et sous-domaines identifiés
- Adresses e-mail et format des identifiants
- Technologies / services devinés
- Plages d'adresses éventuelles
- 3 contre-mesures priorisées

Reporter le résultat dans [`grille-compte-rendu.md`](grille-compte-rendu.md).

## Pour aller plus vite / plus loin

- Ce qui est **passif** ne laisse aucune trace chez la cible : c'est ce qui rend la reconnaissance si
  précieuse (et si difficile à détecter).
- Question de synthèse : parmi vos trouvailles, lesquelles auraient pu être évitées côté défense ?
