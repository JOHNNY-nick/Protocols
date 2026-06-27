# SMB Protocol – Detailed Overview / Présentation Détaillée

> **Bilingual document · Document bilingue — FR 🇫🇷 / EN 🇬🇧**

---

## 1. Introduction

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| **SMB** (Server Message Block) est un protocole réseau de la couche application permettant le **partage de fichiers, d'imprimantes et de ressources** entre machines d'un même réseau. Il fonctionne selon un modèle client-serveur : le client envoie des requêtes, le serveur y répond. Initialement développé par IBM et popularisé par Microsoft sous Windows, SMB est aujourd'hui supporté sur Linux (via Samba) et macOS. | **SMB** (Server Message Block) is an application-layer network protocol that enables **file, printer, and resource sharing** across a network. It operates on a client-server model: the client sends requests and the server responds. Originally developed by IBM and popularised by Microsoft on Windows, SMB is now also supported on Linux (via Samba) and macOS. |

---

## 2. Ports / Ports réseau

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| SMB **moderne** utilise le **port 445 (TCP)** directement sur IP, sans dépendre de NetBIOS. Les anciennes versions (SMBv1 / CIFS) utilisaient les **ports 137–139** via NetBIOS over TCP/IP : 137 (UDP/TCP, résolution de noms NetBIOS), 138 (UDP, datagrammes NetBIOS), 139 (TCP, sessions NetBIOS). | **Modern** SMB runs on **port 445 (TCP)** directly over IP, without relying on NetBIOS. Older versions (SMBv1 / CIFS) used **ports 137–139** via NetBIOS over TCP/IP: 137 (UDP/TCP, NetBIOS name resolution), 138 (UDP, NetBIOS datagrams), 139 (TCP, NetBIOS sessions). |

### Récapitulatif des ports / Port Summary

| Port | Protocole / Protocol | Usage |
|------|----------------------|-------|
| **445** | TCP | SMB moderne / Modern SMB (SMBv2, SMBv3) |
| **139** | TCP | SMB via NetBIOS (SMBv1/CIFS legacy) |
| **138** | UDP | NetBIOS Datagram Service |
| **137** | UDP/TCP | NetBIOS Name Service |

---

## 3. Versions

### Tableau comparatif / Comparison Table

| Version | Nom / Name | Date | 🇫🇷 Caractéristiques | 🇬🇧 Features |
|---------|-----------|------|----------------------|--------------|
| **SMB 1.0** | CIFS (Common Internet File System) | ~1996 | Protocole ancien, verbeux, **non sécurisé**. À l'origine de l'exploitation par WannaCry (EternalBlue). Obsolète. | Legacy protocol, verbose, **insecure**. Exploited by WannaCry (EternalBlue). Now obsolete. |
| **SMB 2.0** | SMB2 | 2006 (Vista) | Réduction drastique du nombre de commandes (19 au lieu de ~100), meilleures performances, support de la mise en cache des crédits. | Drastically reduced command set (19 vs ~100), better performance, credit-based flow control. |
| **SMB 2.1** | SMB2.1 | 2010 (Win 7) | Amélioration du verrouillage opportuniste (oplocks), MTU plus grande. | Improved opportunistic locking, larger MTU support. |
| **SMB 3.0** | SMB3 | 2012 (Win 8) | **Chiffrement de bout en bout** (AES-128-CCM), multicanal (SMB Multichannel), haute disponibilité (SMB Transparent Failover), SMB Direct (RDMA). | **End-to-end encryption** (AES-128-CCM), multipath (SMB Multichannel), high availability (Transparent Failover), SMB Direct (RDMA). |
| **SMB 3.0.2** | SMB3.0.2 | 2013 (Win 8.1) | Possibilité de désactiver SMBv1 via des stratégies de groupe. | Ability to disable SMBv1 via Group Policy. |
| **SMB 3.1.1** | SMB3.1.1 | 2016 (Win 10) | **AES-128-GCM / AES-256**, intégrité de pré-authentification (hachage SHA-512), compression, **SMB over QUIC**. | **AES-128-GCM / AES-256**, pre-authentication integrity (SHA-512 hash), compression, **SMB over QUIC**. |

---

## 4. Syntaxe de connexion / Connection Syntax

### 4.1 `smbclient` — Lister les partages / List Shares

**🇫🇷** Utilisez `smbclient` pour lister les partages disponibles sur un serveur :

**🇬🇧** Use `smbclient` to list available shares on a server:

```bash
# FR : Lister les partages d'un serveur avec authentification utilisateur
# EN : List shares on a server with user authentication
smbclient -L //serveur -U utilisateur
smbclient -L //server  -U user

# FR : Avec un domaine spécifié
# EN : With a specified domain
smbclient -L //serveur -U DOMAINE\\utilisateur
smbclient -L //server  -U DOMAIN\\user

# FR : Sans mot de passe (session anonyme / guest)
# EN : Without password (anonymous / guest session)
smbclient -L //server -N
```

### 4.2 `smbclient` — Se connecter à un partage / Connect to a Share

```bash
# FR : Se connecter au partage "documents" sur le serveur "fileserver"
# EN : Connect to the "documents" share on "fileserver"
smbclient //fileserver/documents -U utilisateur
smbclient //fileserver/documents -U user

# FR : Une fois connecté, commandes disponibles :
# EN : Once connected, available commands:
#   ls           → lister les fichiers / list files
#   get fichier  → télécharger un fichier / download a file
#   put fichier  → envoyer un fichier / upload a file
#   cd dossier   → changer de répertoire / change directory
#   exit         → quitter / quit
```

### 4.3 `mount -t cifs` — Monter un partage / Mount a Share

**🇫🇷** Pour monter un partage SMB en tant que système de fichiers sous Linux :

**🇬🇧** To mount an SMB share as a filesystem on Linux:

```bash
# FR : Monter manuellement
# EN : Manual mount
sudo mount -t cifs //serveur/partage /mnt/point_de_montage \
  -o username=utilisateur,password=motdepasse,vers=3.1.1

# EN : Mount with domain
sudo mount -t cifs //server/share /mnt/mountpoint \
  -o username=user,password=pass,domain=DOMAIN,vers=3.0

# FR : Montage persistant via /etc/fstab
# EN : Persistent mount via /etc/fstab
//server/share  /mnt/mountpoint  cifs  credentials=/etc/samba/.smbcredentials,vers=3.1.1,uid=1000  0 0
```

**🇫🇷** Fichier de credentials `/etc/samba/.smbcredentials` :

**🇬🇧** Credentials file `/etc/samba/.smbcredentials`:

```
username=utilisateur
password=motdepasse
domain=DOMAINE
```

---

## 5. Outils associés / Associated Tools

| Outil / Tool | Plateforme / Platform | 🇫🇷 Description | 🇬🇧 Description |
|---|---|---|---|
| **`smbclient`** | Linux / macOS | Client SMB en ligne de commande fourni par Samba. Permet de parcourir, télécharger et envoyer des fichiers. | Command-line SMB client provided by Samba. Allows browsing, downloading, and uploading files. |
| **`smbutil`** | macOS | Outil natif macOS pour interroger et se connecter à des partages SMB. | Native macOS tool to query and connect to SMB shares. |
| **`smbmap`** | Linux | Outil d'audit permettant de lister les partages et leurs permissions sur un réseau. | Auditing tool to enumerate shares and their permissions across a network. |
| **`enum4linux`** | Linux | Outil d'énumération d'informations SMB/Samba (utilisateurs, groupes, partages). | SMB/Samba enumeration tool (users, groups, shares). |
| **`Samba`** | Linux / Unix | Implémentation open-source du protocole SMB/CIFS. Permet à Linux d'agir comme serveur ou client SMB, compatible avec Windows. | Open-source implementation of the SMB/CIFS protocol. Allows Linux to act as an SMB server or client, fully compatible with Windows. |
| **`net use`** | Windows | Commande Windows pour connecter ou déconnecter des lecteurs réseau SMB. | Windows command to connect or disconnect SMB network drives. |
| **`Get-SmbShare`** | Windows (PowerShell) | Cmdlet PowerShell pour gérer et interroger les partages SMB sur Windows Server. | PowerShell cmdlet to manage and query SMB shares on Windows Server. |

---

## 6. Authentification

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| SMB utilise deux mécanismes d'authentification principaux : **NTLM** (NT LAN Manager) et **Kerberos**. **NTLM** est un protocole challenge-réponse utilisé dans les environnements sans Active Directory ou pour des connexions directes par IP. Il est considéré comme moins sécurisé et vulnérable aux attaques de type Pass-the-Hash. **Kerberos** est le mécanisme préféré dans les environnements **Active Directory (AD)**. Il repose sur des tickets émis par un KDC (Key Distribution Center) et offre une authentification mutuelle, plus sécurisée. | SMB relies on two main authentication mechanisms: **NTLM** (NT LAN Manager) and **Kerberos**. **NTLM** is a challenge-response protocol used in non-Active Directory environments or for direct IP connections. It is considered less secure and is vulnerable to Pass-the-Hash attacks. **Kerberos** is the preferred mechanism in **Active Directory (AD)** environments. It uses tickets issued by a KDC (Key Distribution Center) and provides mutual authentication, making it significantly more secure. |

### Flux d'authentification / Authentication Flow

```
NTLM (simplifié / simplified):
  Client ──NEGOTIATE──► Server
  Client ◄─CHALLENGE──  Server
  Client ──AUTHENTICATE (hash NTLM)──► Server

Kerberos (simplifié / simplified):
  Client ──AS-REQ──► KDC (Domain Controller)
  Client ◄─AS-REP (TGT)── KDC
  Client ──TGS-REQ (TGT)──► KDC
  Client ◄─TGS-REP (Service Ticket)── KDC
  Client ──AP-REQ (Service Ticket)──► SMB Server
```

---

## 7. Sécurité / Security

### Recommandations / Recommendations

| # | 🇫🇷 Recommandation | 🇬🇧 Recommendation |
|---|---|---|
| 1 | **Désactiver SMBv1** — Exploité par EternalBlue (MS17-010), utilisé dans l'attaque mondiale WannaCry (2017). | **Disable SMBv1** — Exploited by EternalBlue (MS17-010), used in the global WannaCry attack (2017). |
| 2 | **Utiliser SMBv3+ avec chiffrement** — Activer le chiffrement de bout en bout (AES-128/256). | **Use SMBv3+ with encryption** — Enable end-to-end encryption (AES-128/256). |
| 3 | **Bloquer SMB sur Internet** — Ne jamais exposer le port 445 directement sur Internet. | **Block SMB from the Internet** — Never expose port 445 directly to the Internet. |
| 4 | **Préférer Kerberos à NTLM** — Désactiver NTLM là où Kerberos est disponible. | **Prefer Kerberos over NTLM** — Disable NTLM where Kerberos is available. |
| 5 | **Activer SMB Signing** — Empêche les attaques de type Man-in-the-Middle (SMB Relay). | **Enable SMB Signing** — Prevents Man-in-the-Middle attacks (SMB Relay). |
| 6 | **Limiter les permissions de partage** — Appliquer le principe du moindre privilège. | **Restrict share permissions** — Apply the principle of least privilege. |
| 7 | **Surveiller les journaux SMB** — Détecter les tentatives d'authentification inhabituelles. | **Monitor SMB logs** — Detect unusual authentication attempts. |

### Désactiver SMBv1 / Disabling SMBv1

```powershell
# Windows — PowerShell (en tant qu'Administrateur / as Administrator)

# FR : Vérifier si SMBv1 est activé
# EN : Check if SMBv1 is enabled
Get-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

# FR : Désactiver SMBv1
# EN : Disable SMBv1
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

# FR : Vérifier l'état côté serveur et client
# EN : Check server and client status
Get-SmbServerConfiguration | Select EnableSMB1Protocol
Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force
```

```bash
# Linux (Samba) — /etc/samba/smb.conf
[global]
  # FR : Forcer SMBv2 minimum
  # EN : Force minimum SMBv2
  min protocol = SMB2
  # FR : Forcer SMBv3.1.1 maximum
  # EN : Force maximum SMBv3.1.1
  max protocol = SMB3
```

---

## 8. Exemple de session / Session Example

**🇫🇷** Voici un exemple complet de session `smbclient` : listage des partages puis connexion à un partage.

**🇬🇧** Here is a complete `smbclient` session example: listing shares, then connecting to one.

### Étape 1 — Lister les partages / Step 1 — List Shares

```
$ smbclient -L //192.168.1.10 -U alice

Enter WORKGROUP\alice's password: ********

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        documents       Disk      Shared documents
        printers        Printer   Office Printers
        IPC$            IPC       Remote IPC

Reconnecting with SMB1 for workgroup listing.
Server               Comment
---------            -------

Workgroup            Master
---------            -------
WORKGROUP            FILESERVER
```

### Étape 2 — Se connecter au partage / Step 2 — Connect to Share

```
$ smbclient //192.168.1.10/documents -U alice

Enter WORKGROUP\alice's password: ********
Try "help" to get a list of possible commands.

smb: \> ls
  .                                   D        0  Fri Jun 27 10:00:00 2025
  ..                                  D        0  Thu Jun 26 08:00:00 2025
  rapport_annuel.pdf                  A   204800  Wed Jun 25 14:32:10 2025
  budget_2025.xlsx                    A    98304  Tue Jun 24 09:15:00 2025
  notes_reunion.docx                  A    45056  Mon Jun 23 16:45:22 2025

                51200 blocks of size 1048576. 32768 blocks available

smb: \> get rapport_annuel.pdf
getting file \rapport_annuel.pdf of size 204800 as rapport_annuel.pdf
(1250.0 KiloBytes/sec) (average 1250.0 KiloBytes/sec)

smb: \> put local_file.txt
putting file local_file.txt as \local_file.txt (120.5 kb/s) (average 120.5 kb/s)

smb: \> exit
```

### Étape 3 — Monter le partage de façon persistante / Step 3 — Mount the Share Persistently

```bash
# FR : Créer le point de montage
# EN : Create the mount point
sudo mkdir -p /mnt/documents

# FR : Monter le partage
# EN : Mount the share
sudo mount -t cifs //192.168.1.10/documents /mnt/documents \
  -o username=alice,vers=3.1.1,uid=$(id -u),gid=$(id -g)

# FR : Vérifier le montage
# EN : Verify the mount
df -h | grep documents
# //192.168.1.10/documents   50G   18G   32G  37% /mnt/documents
```

---

## Résumé / Summary

| Aspect | 🇫🇷 | 🇬🇧 |
|--------|-----|-----|
| **Protocole** | Partage de fichiers/imprimantes en réseau | Network file/printer sharing |
| **Port principal** | 445 TCP (SMBv2/v3) | 445 TCP (SMBv2/v3) |
| **Version recommandée** | SMB 3.1.1 | SMB 3.1.1 |
| **Authentification** | NTLM ou Kerberos (préféré) | NTLM or Kerberos (preferred) |
| **Chiffrement** | AES-128-GCM / AES-256 (SMB 3.1.1) | AES-128-GCM / AES-256 (SMB 3.1.1) |
| **Outil Linux** | `smbclient`, Samba | `smbclient`, Samba |
| **Outil macOS** | `smbutil` | `smbutil` |
| **Sécurité critique** | Désactiver SMBv1 ! | Disable SMBv1! |

---

*Document généré le / Generated on: 2026-06-27*
*Sources: Microsoft Docs, Samba Project, RFC 2744, CVE-2017-0144 (EternalBlue/WannaCry)*
