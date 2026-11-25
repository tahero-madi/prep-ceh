# Jour 1 — Guide d'installation du laboratoire

À réaliser **avant la séance**. Objectif : disposer, sur votre portable, de deux machines virtuelles
(**Kali Linux** = attaquant, **Metasploitable 2** = cible) connectées entre elles en **réseau NAT** :
elles se voient entre elles et gardent un accès Internet, ce qui simplifie l'installation des outils.

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

## 4. Configurer le réseau (full NAT)

On met les deux VM en **NAT** : elles se voient entre elles et gardent un accès Internet, ce qui
simplifie l'installation et la mise à jour des outils de Kali.

> ⚠️ **À savoir.** En NAT, la cible Metasploitable a aussi un accès Internet sortant. C'est un choix
> de simplicité assumé pour ce module. En contrepartie : ne laissez pas le labo tourner sans
> surveillance et **éteignez les VM après usage**.

### 4.1 VirtualBox — « Réseau NAT »

Le *NAT* simple de VirtualBox isole les VM les unes des autres. Pour qu'elles se voient, il faut un
**Réseau NAT** partagé :

1. *Fichier → Outils → Gestionnaire de réseau → onglet Réseaux NAT* (ou *Fichier → Préférences →
   Réseau* selon la version) → **Créer** un réseau NAT nommé `lab-ceh` (ex. `10.0.2.0/24`, DHCP activé).
2. Pour **chaque** VM : *Configuration → Réseau → Adaptateur 1* → *Mode d'accès réseau* : **Réseau
   NAT** → *Nom* : `lab-ceh`.

### 4.2 VMware — « NAT »

Pour **chaque** VM : *VM → Settings → Network Adapter* → **NAT**. Les VM en NAT partagent le même
sous-réseau (VMnet8) : elles se voient et ont Internet. Rien d'autre à configurer.

---

## 5. Démarrer et identifiants

| VM | Identifiant | Mot de passe |
| --- | --- | --- |
| Kali Linux | `kali` | `kali` |
| Metasploitable 2 | `msfadmin` | `msfadmin` |

En NAT, le **DHCP attribue les adresses automatiquement** : aucune configuration IP manuelle. Relever
simplement l'adresse de chaque VM :

```bash
ip a            # sur Kali (interface eth0)
ifconfig        # sur Metasploitable
```

Les deux VM se retrouvent dans le même sous-réseau (ex. `10.0.2.x`).

---

## 6. Vérifier le réseau (avant tout TP)

Depuis **Kali** :

```bash
# 1) Je vois la cible
ping -c 3 <IP_de_Metasploitable>        # doit répondre

# 2) Internet fonctionne (utile pour installer/mettre à jour les outils)
ping -c 3 8.8.8.8                        # répond en NAT
```

Si la cible répond, vous pouvez enchaîner les TP. Si le `ping` vers la cible échoue, vérifier que les
deux VM sont bien sur le même réseau NAT `lab-ceh` (VirtualBox) ou en NAT (VMware).

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
| Kali et la cible ne se pingent pas | Même réseau NAT `lab-ceh` (VirtualBox) ou NAT (VMware) sur les deux VM ? Adaptateurs activés ? |
| Pas d'accès Internet depuis Kali | Vérifier que l'adaptateur est en Réseau NAT / NAT et que le DHCP du réseau NAT est activé |
| Écran noir / lenteur extrême | Réduire la RAM/les cœurs alloués, désactiver l'accélération 3D |

---

## Checklist finale (avant la séance)

- [ ] Kali et Metasploitable importées et démarrables
- [ ] Réseau NAT configuré (`lab-ceh` sur VirtualBox, ou NAT sur VMware)
- [ ] `ping` vers la cible **OK**
- [ ] Snapshot `etat-initial` pris sur les deux VM
- [ ] Identifiants testés
