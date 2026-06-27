# Protocole Telnet – Vue d'ensemble détaillée / Telnet Protocol – Detailed Overview

> **Document bilingue / Bilingual Document — Français (FR) | English (EN)**

---

## Table des matières / Table of Contents

1. [Introduction](#1-introduction)
2. [Port par défaut / Default Port](#2-port-par-défaut--default-port)
3. [Syntaxe de connexion / Connection Syntax](#3-syntaxe-de-connexion--connection-syntax)
4. [Authentification / Authentication](#4-authentification--authentication)
5. [Commandes et usage / Commands and Usage](#5-commandes-et-usage--commands-and-usage)
6. [Erreurs courantes / Common Errors](#6-erreurs-courantes--common-errors)
7. [Sécurité / Security](#7-sécurité--security)
8. [Exemple de session / Session Example](#8-exemple-de-session--session-example)

---

## 1. Introduction

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| **Telnet** (TELecommunication NETwork) est un protocole réseau de la couche application, défini dans la RFC 854 (1983). Il permet d'établir une connexion interactive en ligne de commande vers une machine distante via le réseau TCP/IP. | **Telnet** (TELecommunication NETwork) is an application-layer network protocol, defined in RFC 854 (1983). It enables an interactive command-line connection to a remote machine over a TCP/IP network. |
| Historiquement, Telnet a été l'un des premiers protocoles utilisés pour l'administration à distance des systèmes Unix, des routeurs et des serveurs. Il permettait aux administrateurs de se connecter à une machine distante comme s'ils étaient physiquement présents devant un terminal. | Historically, Telnet was one of the first protocols used for remote administration of Unix systems, routers, and servers. It allowed administrators to connect to a remote machine as if they were physically present at a terminal. |
| **Limitation majeure :** Telnet ne chiffre **aucune** donnée. Les identifiants, mots de passe et toutes les communications transitent en **texte clair** sur le réseau, rendant le protocole vulnérable aux attaques de type *man-in-the-middle* et aux écoutes passives (sniffing). | **Major limitation:** Telnet does **not** encrypt any data. Credentials, passwords, and all communications travel as **plaintext** over the network, making the protocol vulnerable to *man-in-the-middle* attacks and passive eavesdropping (sniffing). |
| Aujourd'hui, Telnet est largement obsolète pour un usage en production et a été remplacé par **SSH** (Secure Shell). Il reste néanmoins utile pour tester la connectivité de ports réseau et déboguer des services. | Today, Telnet is largely obsolete for production use and has been replaced by **SSH** (Secure Shell). It nevertheless remains useful for testing network port connectivity and debugging services. |

---

## 2. Port par défaut / Default Port

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| Telnet utilise le **port TCP 23** par défaut. Lorsqu'aucun port n'est spécifié dans la commande, le client Telnet tente de se connecter automatiquement sur ce port. | Telnet uses **TCP port 23** by default. When no port is specified in the command, the Telnet client automatically attempts to connect on this port. |
| Il est également possible de spécifier un port différent pour tester d'autres services réseau (HTTP sur 80, FTP sur 21, SMTP sur 25, etc.). Cela fait de Telnet un outil pratique de diagnostic réseau. | It is also possible to specify a different port to test other network services (HTTP on 80, FTP on 21, SMTP on 25, etc.). This makes Telnet a handy network diagnostic tool. |

### Ports courants testables avec Telnet / Common Ports Testable with Telnet

| Service | Port | Protocole / Protocol |
|---------|------|----------------------|
| Telnet  | 23   | TCP |
| SSH     | 22   | TCP |
| HTTP    | 80   | TCP |
| HTTPS   | 443  | TCP |
| FTP     | 21   | TCP |
| SMTP    | 25   | TCP |
| POP3    | 110  | TCP |

---

## 3. Syntaxe de connexion / Connection Syntax

### Syntaxe générale / General Syntax

```
telnet <adresse_IP_ou_hôte> <port>
telnet <IP_address_or_hostname> <port>
```

### Exemples / Examples

```bash
# Connexion Telnet standard sur le port par défaut (23)
# Standard Telnet connection on the default port (23)
telnet 10.10.10.5

# Connexion Telnet en spécifiant le port 23 explicitement
# Telnet connection specifying port 23 explicitly
telnet 10.10.10.5 23

# Tester la connectivité HTTP sur le port 80
# Test HTTP connectivity on port 80
telnet 10.10.10.5 80

# Connexion via un nom d'hôte
# Connection using a hostname
telnet myserver.example.com 23
```

### Explication des paramètres / Parameter Explanation

| Paramètre / Parameter | 🇫🇷 Description | 🇬🇧 Description |
|-----------------------|-----------------|-----------------|
| `<adresse_IP>` / `<IP_address>` | Adresse IPv4 ou IPv6 de la machine cible | IPv4 or IPv6 address of the target machine |
| `<hôte>` / `<hostname>` | Nom de domaine ou nom d'hôte résolvable DNS | Domain name or DNS-resolvable hostname |
| `<port>` | Numéro de port TCP (1–65535), optionnel (défaut : 23) | TCP port number (1–65535), optional (default: 23) |

---

## 4. Authentification / Authentication

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| Contrairement à SSH, Telnet **ne prend pas de nom d'utilisateur en argument** dans la ligne de commande. La commande `telnet` ne supporte pas d'option `-u utilisateur` ou similaire. | Unlike SSH, Telnet **does not accept a username as a command-line argument**. The `telnet` command does not support a `-u username` option or similar. |
| Une fois la connexion TCP établie, le service distant envoie un **prompt d'authentification** dans le terminal. Selon le système cible, ce prompt peut s'afficher sous différentes formes. | Once the TCP connection is established, the remote service sends an **authentication prompt** to the terminal. Depending on the target system, this prompt can appear in different forms. |

### Prompts d'authentification courants / Common Authentication Prompts

```
login:
Username:
Login:
User:
```

### Flux d'authentification / Authentication Flow

```
[Client]                          [Serveur / Server]
   |                                      |
   |--- TCP SYN (port 23) -------------> |
   |<-- TCP SYN-ACK ------------------- |
   |--- TCP ACK -----------------------> |
   |                                      |
   |<-- "login: " (texte clair / plain) -|
   |--- "admin\n" --------------------> |
   |<-- "Password: " ------------------ |
   |--- "secret\n" (en clair! / clear!) |
   |<-- Shell prompt "$" / "#" -------- |
```

> ⚠️ **FR :** Le mot de passe transite en **texte clair** sur le réseau.  
> ⚠️ **EN:** The password travels as **plaintext** over the network.

---

## 5. Commandes et usage / Commands and Usage

### Mode interactif Telnet / Telnet Interactive Mode

| 🇫🇷 Français | 🇬🇧 English |
|---|---|
| Une fois connecté, Telnet fonctionne en **mode transparent** : il transmet directement toutes les frappes clavier au service distant et affiche les réponses reçues. Il n'existe pas de jeu de commandes internes comme en FTP. | Once connected, Telnet operates in **transparent mode**: it directly forwards all keystrokes to the remote service and displays the received responses. There is no built-in command set like in FTP. |
| Pour accéder au mode de commande Telnet interne, appuyez sur `Ctrl + ]`. Ce mode permet d'ajuster les paramètres de la connexion. | To access the internal Telnet command mode, press `Ctrl + ]`. This mode allows you to adjust connection parameters. |

### Commandes Telnet internes / Internal Telnet Commands

| Commande / Command | 🇫🇷 Description | 🇬🇧 Description |
|--------------------|-----------------|-----------------|
| `open <hôte> <port>` | Ouvrir une connexion vers un hôte et un port spécifiés | Open a connection to a specified host and port |
| `close` | Fermer la connexion active sans quitter Telnet | Close the active connection without exiting Telnet |
| `quit` / `exit` | Quitter le client Telnet complètement | Exit the Telnet client completely |
| `status` | Afficher l'état actuel de la connexion | Display the current connection status |
| `set` | Configurer des options de la session (ex. mode, echo) | Configure session options (e.g., mode, echo) |
| `unset` | Désactiver une option précédemment définie | Disable a previously set option |
| `display` | Afficher les paramètres actuels de la session | Show current session parameters |
| `send <arg>` | Envoyer des séquences de contrôle Telnet (IAC) | Send Telnet control sequences (IAC) |
| `?` / `help` | Afficher l'aide des commandes disponibles | Display help for available commands |

### Usage en diagnostic réseau / Network Diagnostic Usage

```bash
# Tester si un port HTTP est ouvert / Test if an HTTP port is open
telnet www.example.com 80

# Une fois connecté, envoyer une requête HTTP brute
# Once connected, send a raw HTTP request
GET / HTTP/1.1
Host: www.example.com
[Entrée deux fois / Press Enter twice]

# Tester un serveur SMTP / Test an SMTP server
telnet mail.example.com 25
```

> **FR :** L'interaction avec Telnet est **brute (raw)** — vous saisissez directement le protocole du service cible.  
> **EN:** Interaction with Telnet is **raw** — you type the target service's protocol directly.

---

## 6. Erreurs courantes / Common Errors

### Tableau des erreurs / Error Table

| Erreur / Error | 🇫🇷 Cause & Solution | 🇬🇧 Cause & Solution |
|----------------|----------------------|----------------------|
| `Server lookup failure: root:telnet` | `root` est interprété comme un **nom d'hôte** au lieu d'un utilisateur. Telnet ne prend pas de nom d'utilisateur en argument. | `root` is interpreted as a **hostname** instead of a username. Telnet does not accept a username as an argument. |
| `Connection refused` | Le port est fermé ou le service n'est pas en cours d'exécution sur la cible. | The port is closed or the service is not running on the target. |
| `Connection timed out` | L'hôte est inaccessible (pare-feu, routage, hôte éteint). | The host is unreachable (firewall, routing, host powered off). |
| `telnet: command not found` | Le client Telnet n'est pas installé sur le système. | The Telnet client is not installed on the system. |
| `Name or service not known` | Le nom d'hôte ne peut pas être résolu par le DNS. | The hostname cannot be resolved by DNS. |

### Erreur spécifique : `Server lookup failure: root:telnet`

**Commande erronée / Wrong command:**
```bash
telnet root@10.10.10.5
# ❌ Erreur : "Server lookup failure: root:telnet" ou similaire
# ❌ Error  : "Server lookup failure: root:telnet" or similar
```

| 🇫🇷 Explication | 🇬🇧 Explanation |
|-----------------|-----------------|
| Telnet n'accepte pas la syntaxe `utilisateur@hôte`. Lorsque vous tapez `telnet root@10.10.10.5`, Telnet interprète `root@10.10.10.5` comme un **nom d'hôte** entier, ce qui échoue à la résolution DNS. | Telnet does not accept the `user@host` syntax. When you type `telnet root@10.10.10.5`, Telnet interprets `root@10.10.10.5` as an entire **hostname**, which fails DNS resolution. |

**Commande correcte / Correct command:**
```bash
telnet 10.10.10.5 23
# ✅ Le nom d'utilisateur est saisi après connexion, au prompt "login:"
# ✅ The username is entered after connection, at the "login:" prompt
```

### Installation du client Telnet / Installing the Telnet Client

```bash
# Debian / Ubuntu
sudo apt install telnet

# Red Hat / CentOS / Fedora
sudo dnf install telnet

# macOS (via Homebrew)
brew install telnet

# Windows (PowerShell, en tant qu'administrateur / as Administrator)
dism /online /Enable-Feature /FeatureName:TelnetClient
```

---

## 7. Sécurité / Security

### Comparaison Telnet vs SSH / Telnet vs SSH Comparison

| Critère / Criterion | Telnet | SSH (Secure Shell) |
|---------------------|--------|--------------------|
| **Port par défaut / Default port** | 23 | 22 |
| **Chiffrement / Encryption** | ❌ Aucun / None | ✅ AES, ChaCha20, etc. |
| **Authentification / Authentication** | Texte clair / Plaintext | Clés publiques, mots de passe chiffrés / Public keys, encrypted passwords |
| **Intégrité des données / Data integrity** | ❌ Non garantie / Not guaranteed | ✅ HMAC |
| **Résistance MITM / MITM resistance** | ❌ Vulnérable / Vulnerable | ✅ Vérification d'empreinte / Fingerprint verification |
| **Usage recommandé / Recommended use** | Tests et diagnostic uniquement / Testing and diagnosis only | Administration distante / Remote administration |
| **RFC** | RFC 854 (1983) | RFC 4251 (2006) |

### Risques de sécurité Telnet / Telnet Security Risks

| 🇫🇷 Risque | 🇬🇧 Risk |
|------------|----------|
| **Sniffing réseau :** Toute personne ayant accès au réseau peut capturer le trafic Telnet avec des outils comme Wireshark et lire les identifiants en clair. | **Network sniffing:** Anyone with network access can capture Telnet traffic using tools like Wireshark and read credentials in plaintext. |
| **Attaque Man-in-the-Middle :** Sans chiffrement ni vérification d'intégrité, un attaquant peut intercepter et modifier les données en transit. | **Man-in-the-Middle attack:** Without encryption or integrity verification, an attacker can intercept and modify data in transit. |
| **Rejeu de session :** Les paquets capturés peuvent être rejoués pour usurper une session authentifiée. | **Session replay:** Captured packets can be replayed to impersonate an authenticated session. |

### Recommandation / Recommendation

> 🇫🇷 **Utilisez SSH à la place de Telnet** pour toute administration distante. SSH chiffre l'intégralité de la session, authentifie le serveur via une empreinte de clé et supporte l'authentification par clé publique (sans mot de passe).
>
> 🇬🇧 **Use SSH instead of Telnet** for all remote administration. SSH encrypts the entire session, authenticates the server via key fingerprint, and supports public-key authentication (passwordless).

```bash
# Équivalent SSH de `telnet 10.10.10.5`
# SSH equivalent of `telnet 10.10.10.5`
ssh user@10.10.10.5

# Avec un port spécifique / With a specific port
ssh -p 2222 user@10.10.10.5
```

---

## 8. Exemple de session / Session Example

### Scénario / Scenario

| 🇫🇷 | 🇬🇧 |
|-----|-----|
| Connexion d'un client vers un serveur Linux à l'adresse `10.10.10.5` sur le port Telnet par défaut (23), suivi d'une authentification et d'une commande de base. | Connection from a client to a Linux server at address `10.10.10.5` on the default Telnet port (23), followed by authentication and a basic command. |

### Transcript de session / Session Transcript

```
$ telnet 10.10.10.5 23

Trying 10.10.10.5...
Connected to 10.10.10.5.
Escape character is '^]'.

  ██████╗ ███████╗███╗   ███╗ ██████╗ ████████╗███████╗
  ██╔══██╗██╔════╝████╗ ████║██╔═══██╗╚══██╔══╝██╔════╝
  ██████╔╝█████╗  ██╔████╔██║██║   ██║   ██║   █████╗
  ██╔══██╗██╔══╝  ██║╚██╔╝██║██║   ██║   ██║   ██╔══╝
  ██║  ██║███████╗██║ ╚═╝ ██║╚██████╔╝   ██║   ███████╗
  ╚═╝  ╚═╝╚══════╝╚═╝     ╚═╝ ╚═════╝    ╚═╝   ╚══════╝

  Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0 kernel)

login: admin
Password:
Last login: Fri Jun 27 10:22:01 2025 from 192.168.1.100

admin@remote-server:~$ whoami
admin

admin@remote-server:~$ ls -la
total 32
drwxr-xr-x 4 admin admin 4096 Jun 27 10:22 .
drwxr-xr-x 3 root  root  4096 Jun 25 08:00 ..
-rw-r--r-- 1 admin admin  220 Jun 25 08:00 .bash_logout
-rw-r--r-- 1 admin admin 3526 Jun 25 08:00 .bashrc
-rw-r--r-- 1 admin admin  807 Jun 25 08:00 .profile
drwx------ 2 admin admin 4096 Jun 25 08:00 .ssh
drwxr-xr-x 2 admin admin 4096 Jun 27 09:15 documents

admin@remote-server:~$ exit
logout

Connection closed by foreign host.
$
```

### Annotations du transcript / Transcript Annotations

| Ligne / Line | 🇫🇷 Signification | 🇬🇧 Meaning |
|--------------|-------------------|-------------|
| `Trying 10.10.10.5...` | Telnet tente d'établir la connexion TCP | Telnet is attempting to establish the TCP connection |
| `Connected to 10.10.10.5.` | Connexion TCP établie avec succès | TCP connection successfully established |
| `Escape character is '^]'.` | `Ctrl+]` permet d'entrer en mode commande Telnet | `Ctrl+]` enters the Telnet command mode |
| `login:` | Prompt d'authentification envoyé par le serveur (en clair) | Authentication prompt sent by the server (in plaintext) |
| `Password:` | Le mot de passe est demandé — **transmis en clair !** | Password is requested — **transmitted in plaintext!** |
| `admin@remote-server:~$` | Shell distant actif, session établie | Remote shell active, session established |
| `Connection closed by foreign host.` | Le serveur a fermé la connexion après `exit` | The server closed the connection after `exit` |

### Quitter Telnet sans commande `exit` / Exiting Telnet Without `exit` Command

```
# Depuis le shell distant, presser Ctrl+] pour entrer en mode commande Telnet
# From the remote shell, press Ctrl+] to enter Telnet command mode

^]

telnet> quit
Connection closed.
$
```

---

## Résumé / Summary

| Aspect | 🇫🇷 Résumé | 🇬🇧 Summary |
|--------|-----------|-------------|
| **Protocole** | Couche application, TCP | Application layer, TCP |
| **Port** | 23 (par défaut) | 23 (default) |
| **Chiffrement** | Aucun — texte clair | None — plaintext |
| **Utilisation moderne** | Diagnostic réseau, tests de ports | Network diagnostics, port testing |
| **Remplacement** | SSH (port 22) | SSH (port 22) |
| **RFC** | RFC 854, RFC 855 | RFC 854, RFC 855 |

---

> **FR :** Ce document est fourni à des fins éducatives et de diagnostic réseau. L'utilisation de Telnet pour administrer des systèmes en production est fortement déconseillée. Privilégiez **SSH** pour toute connexion distante sécurisée.
>
> **EN:** This document is provided for educational and network diagnostic purposes. Using Telnet to administer production systems is strongly discouraged. Use **SSH** for all secure remote connections.
