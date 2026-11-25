# Jour 1 — Guide d'installation du laboratoire

À réaliser **avant la séance**. Objectif : disposer, sur votre portable, de deux machines virtuelles
(**Kali Linux** = attaquant, **Metasploitable 2** = cible) connectées entre elles dans un **réseau
isolé**, sans aucun accès à Internet ni au réseau de l'école.

Temps estimé : 45 à 60 min (téléchargements compris).

---

## 1. Prérequis

- PC portable avec **VMware Workstation/Player** ou **VirtualBox** installé.
- 8 Go de RAM minimum (16 Go confortables), 60 Go de disque libre.
- **Virtualisation activée dans le BIOS/UEFI** (VT-x pour Intel, AMD-V pour AMD).
  - Vérifier sous Windows : *Gestionnaire des tâches → Performance → Processeur →* « Virtualisation : Activé ».
  - Si « Désactivé » : redémarrer, entrer dans le BIOS (F2/F10/Suppr selon la marque), activer
    *Intel Virtualization Technology* ou *SVM Mode*.

> 💡 **Windows + VirtualBox** : si les VM ne démarrent qu'en 32 bits ou plantent, désactiver Hyper-V :
> `bcdedit /set hypervisorlaunchtype off` dans une invite **administrateur**, puis redémarrer.
> (Pour réactiver plus tard : `bcdedit /set hypervisorlaunchtype auto`.)

---

## 2. Télécharger les images

| Machine | Rôle | Source |
| --- | --- | --- |
| **Kali Linux** (image VM préconstruite) | Attaquant | `kali.org` → *Get Kali* → *Virtual Machines* (choisir VMware **ou** VirtualBox) |
| **Metasploitable 2** | Cible Linux vulnérable | [sourceforge.net/projects/metasploitable](https://sourceforge.net/projects/metasploitable/) (`metasploitable-linux-2.0.0.zip`) |

Télécharger l'image **Kali** correspondant à votre hyperviseur (archive `.7z` VMware ou `.7z` VirtualBox).
Metasploitable 2 est une archive contenant un fichier `.vmdk` utilisable par les deux hyperviseurs.

> Vérifiez l'empreinte **SHA-256** des téléchargements quand elle est fournie : c'est un réflexe
> d'hygiène, et un premier exemple concret d'intégrité (un des piliers de la triade CIA).

---

## 3. Importer les machines

### 3.1 VMware Workstation / Player

**Kali** : décompresser l'archive, puis *File → Open* et sélectionner le fichier `.vmx`.

**Metasploitable 2** :
1. Décompresser `metasploitable-linux-2.0.0.zip`.
2. *File → Open* → sélectionner `Metasploitable.vmx`.
3. Si VMware propose « I Copied It », choisir **I Copied It**.

### 3.2 VirtualBox

**Kali** : décompresser l'archive VirtualBox, puis double-cliquer le fichier `.vbox` (ou *Machine →
Ajouter*).

**Metasploitable 2** (pas de `.vbox` fourni, on crée la VM autour du disque) :
1. *Machine → Nouvelle* : nom `Metasploitable2`, type **Linux**, version **Ubuntu (64-bit)**, 512–1024 Mo de RAM.
2. *Disque dur* → **Utiliser un fichier de disque dur virtuel existant** → sélectionner le `.vmdk` décompressé.
3. Créer.

---

## 4. Configurer le réseau isolé (étape critique)

Les deux VM doivent se voir **entre elles**, mais **ni Internet, ni le réseau de l'école**. On
supprime donc tout adaptateur NAT ou ponté (bridged).

### 4.1 VirtualBox — « Réseau interne »

Pour **chaque** VM (Kali et Metasploitable) : *Configuration → Réseau → Adaptateur 1* :
- **Activer** l'adaptateur réseau
- *Mode d'accès réseau* : **Réseau interne**
- *Nom* : `lab-ceh` (exactement le même nom sur les deux VM)
- Désactiver tout autre adaptateur (2, 3, 4)

### 4.2 VMware — « LAN Segment »

Pour **chaque** VM : *VM → Settings → Network Adapter* :
- **Custom** → *LAN Segment* → créer `lab-ceh` et l'affecter aux deux VM
- Décocher *Connect at power on* pour tout adaptateur NAT/Bridged, ou le retirer

> 🔒 **Règle d'or du cours** : si une VM a besoin d'Internet (mise à jour de Kali, installation d'un
> outil), on bascule temporairement **Kali seul** en NAT, on fait la mise à jour, puis on **repasse en
> réseau isolé** avant tout TP. Metasploitable, elle, ne doit **jamais** voir Internet.

---

## 5. Démarrer et identifiants

| VM | Identifiant | Mot de passe |
| --- | --- | --- |
| Kali Linux | `kali` | `kali` |
| Metasploitable 2 | `msfadmin` | `msfadmin` |

Donner à chaque VM une IP dans le même sous-réseau. Le plus simple : laisser Metasploitable prendre
son IP automatiquement, et fixer Kali dans le même `/24`. Exemple manuel sur Kali :

```bash
sudo ip addr add 192.168.56.10/24 dev eth0
sudo ip link set eth0 up
```

Sur Metasploitable, relever l'IP avec `ifconfig` (souvent `192.168.56.x` ou `10.0.x.x`) et aligner
Kali sur le même sous-réseau.

---

## 6. Vérifier l'isolement (obligatoire avant tout TP)

Depuis **Kali** :

```bash
# 1) Je vois la cible
ping -c 3 <IP_de_Metasploitable>        # doit répondre

# 2) Je ne vois PAS Internet
ping -c 3 8.8.8.8                        # doit échouer (100% packet loss)
```

Les deux conditions doivent être vraies : la cible répond, Internet est injoignable. Si `8.8.8.8`
répond, le réseau n'est pas isolé → revoir l'étape 4 **avant** de continuer.

---

## 7. Prendre un snapshot « état initial »

Un snapshot permet de tout remettre à zéro en 30 secondes si une VM est cassée pendant un TP.

- **VirtualBox** : *Machine → Prendre un instantané* → nommer `etat-initial`.
- **VMware** : *VM → Snapshot → Take Snapshot* → nommer `etat-initial`.

À faire sur **les deux VM**, cible comprise.

---

## 8. Dépannage express

| Symptôme | Piste |
| --- | --- |
| La VM ne démarre qu'en 32 bits | Virtualisation désactivée dans le BIOS, ou Hyper-V actif (Windows) |
| « VT-x is disabled » | Activer VT-x/AMD-V dans le BIOS |
| Kali et la cible ne se pingent pas | Même nom de réseau interne / LAN Segment sur les deux ? Adaptateurs activés ? IP dans le même `/24` ? |
| `8.8.8.8` répond | Un adaptateur NAT/bridged est resté actif → le désactiver |
| Écran noir / lenteur extrême | Réduire la RAM/les cœurs alloués, désactiver l'accélération 3D |

---

## Checklist finale (avant la séance)

- [ ] Kali et Metasploitable importées et démarrables
- [ ] Réseau isolé configuré (réseau interne / LAN Segment `lab-ceh`)
- [ ] `ping` cible **OK**, `ping 8.8.8.8` **KO**
- [ ] Snapshot `etat-initial` pris sur les deux VM
- [ ] Identifiants testés
