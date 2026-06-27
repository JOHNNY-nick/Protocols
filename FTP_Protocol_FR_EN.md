# FTP Protocol – Detailed Overview / Aperçu Détaillé du Protocole FTP

> 🇫🇷 **Français** | 🇬🇧 **English**

---

## 1. Introduction

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| **FTP** (File Transfer Protocol) est un protocole réseau standard permettant le transfert de fichiers entre un client et un serveur sur un réseau TCP/IP. Il a été défini pour la première fois dans les années 1970 (RFC 114, puis RFC 959 en 1985). Historiquement, il a été l'un des premiers protocoles utilisés sur Internet pour partager des fichiers. Aujourd'hui, il reste utilisé dans des environnements internes, des hébergements web et des systèmes embarqués, bien qu'il soit progressivement remplacé par des alternatives plus sécurisées. | **FTP** (File Transfer Protocol) is a standard network protocol used to transfer files between a client and a server over a TCP/IP network. It was first defined in the early 1970s (RFC 114, later RFC 959 in 1985). Historically, it was one of the first protocols used on the Internet for file sharing. Today, it remains in use in internal environments, web hosting, and embedded systems, though it is gradually being replaced by more secure alternatives. |

---

## 2. Ports / Ports

| Port | Rôle 🇫🇷 | Role 🇬🇧 | Mode |
|------|-----------|----------|------|
| **21** | Canal de **contrôle** — échange des commandes et réponses entre client et serveur | **Control** channel — exchanges commands and responses between client and server | Actif & Passif / Active & Passive |
| **20** | Canal de **données** — transfert effectif des fichiers | **Data** channel — actual file transfer | Actif uniquement / Active only |

### Modes de connexion / Connection Modes

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| **Mode Actif** : le client ouvre un port aléatoire et le serveur initie la connexion de données vers ce port depuis le port 20. | **Active Mode**: the client opens a random port and the server initiates the data connection to that port from port 20. |
| **Mode Passif** : le serveur ouvre un port aléatoire et le client initie la connexion de données. Contourne les problèmes de pare-feu côté client. | **Passive Mode**: the server opens a random port and the client initiates the data connection. Bypasses client-side firewall issues. |

```
Active Mode:                        Passive Mode:
Client ──[cmd:21]──► Server         Client ──[cmd:21]──► Server
Server ──[data:20]──► Client        Client ──[data:XXXX]──► Server
```

---

## 3. Connexion / Connection

### Syntaxe / Syntax

```bash
# Syntaxe générale / General syntax
ftp <IP_ADDRESS> <PORT>

# Exemple / Example — port par défaut 21 / default port 21
ftp 192.168.1.10

# Exemple / Example — port personnalisé / custom port
ftp 192.168.1.10 2121
```

### Connexion anonyme / Anonymous Login

```bash
$ ftp 192.168.1.10
Connected to 192.168.1.10.
220 Welcome to the FTP server.
Name (192.168.1.10:user): anonymous
331 Please specify the password.
Password: [vide / leave blank, or enter your email]
230 Login successful.
ftp>
```

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| La connexion FTP s'établit via TCP sur le port 21. Après connexion, le serveur envoie un code **220** indiquant qu'il est prêt. Le client envoie ensuite son identifiant et mot de passe. | The FTP connection is established via TCP on port 21. After connecting, the server sends a **220** code indicating it is ready. The client then sends its username and password. |

---

## 4. Authentification / Authentication

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| Le compte **`anonymous`** est un utilisateur spécial présent sur de nombreux serveurs FTP publics. Il permet un accès en lecture seule sans nécessiter de compte personnel. | The **`anonymous`** account is a special user available on many public FTP servers. It allows read-only access without requiring a personal account. |
| Le mot de passe est généralement **vide** ou une **adresse e-mail** (par convention). Le serveur accepte souvent n'importe quelle chaîne de caractères. | The password is typically **blank** or an **email address** (by convention). The server usually accepts any string. |
| Pour les comptes personnels, un nom d'utilisateur et un mot de passe spécifiques sont requis, transmis **en clair** sur le réseau (non chiffrés). | For personal accounts, a specific username and password are required, transmitted **in plain text** over the network (unencrypted). |

```
Username : anonymous
Password : (blank) OR user@example.com
```

---

## 5. Commandes Principales / Main Commands

| Commande | 🇫🇷 Description | 🇬🇧 Description | Exemple / Example |
|----------|-----------------|-----------------|-------------------|
| `ls` | Lister les fichiers et dossiers du répertoire courant | List files and folders in the current directory | `ftp> ls` |
| `cd <dir>` | Changer de répertoire sur le serveur | Change directory on the server | `ftp> cd pub` |
| `lcd <dir>` | Changer de répertoire **local** | Change **local** directory | `ftp> lcd /tmp` |
| `get <file>` | Télécharger un fichier unique depuis le serveur | Download a single file from the server | `ftp> get readme.txt` |
| `mget <pattern>` | Télécharger plusieurs fichiers (jokers acceptés) | Download multiple files (wildcards accepted) | `ftp> mget *.txt` |
| `put <file>` | Envoyer un fichier local vers le serveur | Upload a local file to the server | `ftp> put report.pdf` |
| `mput <pattern>` | Envoyer plusieurs fichiers vers le serveur | Upload multiple files to the server | `ftp> mput *.csv` |
| `pwd` | Afficher le répertoire courant sur le serveur | Print current working directory on the server | `ftp> pwd` |
| `mkdir <dir>` | Créer un répertoire sur le serveur | Create a directory on the server | `ftp> mkdir backup` |
| `delete <file>` | Supprimer un fichier sur le serveur | Delete a file on the server | `ftp> delete old.log` |
| `binary` | Passer en mode transfert binaire (pour les fichiers non-texte) | Switch to binary transfer mode (for non-text files) | `ftp> binary` |
| `ascii` | Passer en mode transfert texte | Switch to ASCII (text) transfer mode | `ftp> ascii` |
| `bye` / `quit` | Fermer la session FTP et quitter | Close the FTP session and exit | `ftp> bye` |

---

## 6. Codes de Réponse FTP / FTP Response Codes

| Code | 🇫🇷 Signification | 🇬🇧 Meaning | Catégorie |
|------|-------------------|-------------|-----------|
| **220** | Service prêt — le serveur est disponible | Service ready — server is available | ✅ Succès / Success |
| **221** | Service fermé — déconnexion en cours | Service closing — goodbye | ✅ Succès / Success |
| **227** | Mode passif activé (adresse et port fournis) | Entering Passive Mode (address and port provided) | ✅ Succès / Success |
| **230** | Connexion réussie — utilisateur authentifié | Login successful — user logged in | ✅ Succès / Success |
| **250** | Action demandée complétée avec succès | Requested file action completed successfully | ✅ Succès / Success |
| **257** | Répertoire créé ou affiché | Directory created or pathname shown | ✅ Succès / Success |
| **331** | Nom d'utilisateur OK — mot de passe requis | Username OK — password needed | 🔄 Intermédiaire / Intermediate |
| **425** | Impossible d'ouvrir la connexion de données | Cannot open data connection | ❌ Erreur / Error |
| **530** | Accès refusé — utilisateur non connecté | Not logged in — access denied | ❌ Erreur / Error |
| **550** | Action refusée — fichier non disponible | Action not taken — file unavailable | ❌ Erreur / Error |

### Catégories de codes / Code Categories

| Série | 🇫🇷 Signification | 🇬🇧 Meaning |
|-------|-------------------|-------------|
| **1xx** | Réponse préliminaire positive | Positive preliminary reply |
| **2xx** | Réponse positive (action complétée) | Positive completion reply |
| **3xx** | Réponse positive intermédiaire (action en attente) | Positive intermediate reply |
| **4xx** | Réponse négative transitoire (réessayer) | Transient negative reply (try again) |
| **5xx** | Réponse négative permanente | Permanent negative reply |

---

## 7. Sécurité / Security

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| FTP est un protocole **non chiffré** : les identifiants, mots de passe et données transitent **en clair** sur le réseau. Il est donc vulnérable aux attaques de type **sniffing** (écoute passive) et **man-in-the-middle**. | FTP is an **unencrypted** protocol: credentials, passwords, and data travel **in plain text** over the network. It is therefore vulnerable to **sniffing** (passive eavesdropping) and **man-in-the-middle** attacks. |

### Alternatives sécurisées / Secure Alternatives

| Protocole | 🇫🇷 Description | 🇬🇧 Description | Port |
|-----------|-----------------|-----------------|------|
| **FTPS** (FTP Secure) | FTP avec chiffrement **TLS/SSL** ajouté | FTP with added **TLS/SSL** encryption | 990 (implicite) / 21 (explicite) |
| **SFTP** (SSH File Transfer Protocol) | Protocole de transfert de fichiers basé sur **SSH**, entièrement différent de FTP | SSH-based file transfer protocol, entirely different from FTP | 22 |
| **SCP** (Secure Copy Protocol) | Copie sécurisée de fichiers via **SSH** | Secure file copy over **SSH** | 22 |
| **HTTPS** | Transfert via HTTP chiffré pour téléchargements web | Encrypted HTTP transfer for web downloads | 443 |

> ⚠️ **🇫🇷** Il est fortement recommandé d'utiliser **SFTP** ou **FTPS** plutôt que FTP dans tout environnement de production.  
> ⚠️ **🇬🇧** It is strongly recommended to use **SFTP** or **FTPS** instead of FTP in any production environment.

---

## 8. Exemple de Session / Example Session

> 🇫🇷 Voici une session FTP complète avec authentification anonyme et téléchargement d'un fichier.  
> 🇬🇧 Below is a complete FTP session with anonymous authentication and file download.

```
$ ftp 192.168.1.10
Connected to 192.168.1.10.
220 (vsFTPd 3.0.3) — Service ready.             # Server is ready / Serveur prêt

Name (192.168.1.10:kali): anonymous
331 Please specify the password.                 # Password required / Mot de passe requis

Password: user@example.com
230 Login successful.                            # Authenticated / Authentifié
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls                                          # List files / Lister les fichiers
200 PORT command successful.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             1024 Jan 01 12:00 readme.txt
-rw-r--r--    1 0        0            20480 Jan 01 12:01 data.zip
drwxr-xr-x    2 0        0             4096 Jan 01 12:02 pub
226 Directory send OK.

ftp> cd pub                                      # Change directory / Changer de répertoire
250 Directory successfully changed.

ftp> ls
150 Here comes the directory listing.
-rw-r--r--    1 0        0             5120 Jan 01 12:03 notes.txt
226 Directory send OK.

ftp> binary                                      # Set binary mode / Mode binaire
200 Switching to Binary mode.

ftp> get notes.txt                               # Download file / Télécharger le fichier
local: notes.txt remote: notes.txt
150 Opening BINARY mode data connection.
226 Transfer complete.                           # Transfer done / Transfert terminé
5120 bytes received in 0.01 secs (512.0 kB/s)

ftp> pwd                                         # Current directory / Répertoire courant
257 "/pub" is the current directory

ftp> bye                                         # Quit / Quitter
221 Goodbye.
$
```

### Résumé de la session / Session Summary

| Étape / Step | Commande | Code | 🇫🇷 Résultat | 🇬🇧 Result |
|---|---|---|---|---|
| Connexion au serveur | *(auto)* | 220 | Serveur prêt | Server ready |
| Envoi du nom d'utilisateur | `anonymous` | 331 | Mot de passe demandé | Password requested |
| Envoi du mot de passe | `user@example.com` | 230 | Connecté | Logged in |
| Listage des fichiers | `ls` | 150 / 226 | Fichiers listés | Files listed |
| Changement de dossier | `cd pub` | 250 | Dossier changé | Directory changed |
| Mode binaire | `binary` | 200 | Mode activé | Mode set |
| Téléchargement | `get notes.txt` | 150 / 226 | Fichier téléchargé | File downloaded |
| Déconnexion | `bye` | 221 | Session fermée | Session closed |

---

## Références / References

- RFC 959 – File Transfer Protocol (FTP): https://www.rfc-editor.org/rfc/rfc959
- RFC 2228 – FTP Security Extensions (FTPS): https://www.rfc-editor.org/rfc/rfc2228
- RFC 4253 – SSH Transport Layer Protocol (SFTP basis): https://www.rfc-editor.org/rfc/rfc4253

---

*Document généré par Claude · Generated by Claude — Anthropic*  
*Format : Markdown bilingue FR/EN · Bilingual Markdown FR/EN*
