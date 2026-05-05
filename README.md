# 🌐 Infrastructure Réseau Multi-Sites – ATP World Tour

> MSPR Bloc 5 – Concevoir et sécuriser des solutions d'infrastructures virtualisées et cloud  
> Certification Expert Informatique SI – RNCP Niveau 7 | EPSI

---

## 📋 Contexte du projet

Dans le cadre de la certification RNCP Niveau 7, ce projet consiste à concevoir et déployer 
une infrastructure réseau complète et sécurisée pour l'**Association of Tennis Professionals 
(ATP)**, organisation mondiale gérant plus de 64 tournois dans 31 pays.

L'ATP disposait d'un SI fragmenté avec des entités indépendantes à Londres (300 salariés), 
Monaco (150), Floride/USA (50) et Sydney (200). L'objectif était d'**uniformiser et 
sécuriser l'ensemble du SI** tout en préparant une migration vers le cloud.

---

## 🏗️ Architecture déployée

### Sites et plan d'adressage

| Site | VLAN Admin | VLAN Users | Firewall |
|------|-----------|------------|---------|
| Londres (siège) | 192.168.1.0/24 (VLAN 11) | 192.168.10.0/24 (VLAN 10) | pfSense UK |
| Monaco (PRA) | 192.168.3.0/24 (VLAN 21) | 192.168.30.0/24 (VLAN 20) | pfSense Monaco |
| USA – Floride | 192.168.4.0/24 (VLAN 31) | 192.168.40.0/24 (VLAN 30) | pfSense US |
| Australie – Sydney | 192.168.2.0/24 (VLAN 41) | 192.168.20.0/24 (VLAN 40) | pfSense AUS |
| Datacenter cloud | 13.13.13.0/24 (VLAN 99) | — | pfSense DC |

### Machines virtuelles déployées (VMware ESXi)

23 VMs déployées sur un hyperviseur unique (45 Go RAM) :
- 5 × pfSense (firewalls/routeurs par site)
- 3 × Windows Server 2025 (Active Directory + DNS + DHCP)
- 1 × Ubuntu Server (Nextcloud – partage de fichiers)
- 1 × Debian Server (Centreon – supervision)
- 4 × Windows 10/11 (postes utilisateurs par site)
- 1 × Client nomade (test OpenVPN)

---

## ✅ Missions réalisées

### T1 – Infrastructure de virtualisation
- Déploiement VMware ESXi avec vSwitchs dédiés par site
- Segmentation réseau par VLANs (Admin/Users par site)
- Optimisation des ressources sur hyperviseur unique

### T2 – Identification centralisée & Bureaux virtuels
- Active Directory avec domaine `atp.local`
- Contrôleur de domaine principal à Londres + réplication USA
- Service RDP (Remote Desktop Services) pour utilisateurs nomades
- DNS redondant intégré aux contrôleurs AD

### T3 – Plateforme de partage de fichiers
- Déploiement **Nextcloud** sur Ubuntu Server (LAMP : Apache, MariaDB, PHP)
- Partage de fichiers via lien web, synchronisation client, groupes LDAP/AD
- Réplication vers site Monaco pour le PCA

### T4 – Protection firewall & VPN inter-sites
- Pare-feux **pfSense** sur chaque site avec règles de filtrage (principe du moindre privilège)
- **Tunnels VPN IPSec site-à-site** entre tous les sites (AES-256, SHA-256, IKEv2)
- Interconnexion sécurisée : Londres ↔ Monaco ↔ USA ↔ Australie

### T5 – Accès utilisateurs nomades
- **OpenVPN** déployé sur pfSense Londres (mode Remote Access, SSL/TLS)
- Création de CA et certificats dédiés
- Test de connexion nomade validé avec accès au SI interne

### T6 – Supervision & Alerting
- Déploiement **Centreon** (basé Nagios) sur Debian Server
- Supervision : disponibilité services, CPU/RAM/disque, métrologie réseau SNMP
- Alertes e-mail configurées avec seuils (CPU > 80%, disque < 10%)
- Tableau de bord centralisé

### T7 – Plan de Reprise d'Activité (PRA)
- Site de secours : **Monaco**
- Serveurs dormants : AD répliqué, Nextcloud, RDP
- Tunnel VPN niveau 2 pour conserver le plan d'adressage IP
- RPO cible : 36h | RTO cible : 48h

---

## 🔒 Partie Conseil & R&D

| Mission | Contenu |
|---------|---------|
| C1 – Migration Cloud | Recommandation **Microsoft Azure** (lift & shift) avec analyse CAPEX/OPEX |
| C2 – UCaaS | Recommandation **Microsoft Teams + Teams Phone System** |
| C3 – SSO Cloud | Étude **Azure AD Connect** avec MFA et accès conditionnel |
| C4 – Procédure PRA | Analyse de 10 risques critiques + procédure de bascule documentée |

---

## 🛠️ Stack technique

| Catégorie | Technologies |
|-----------|-------------|
| Virtualisation | VMware ESXi, vSphere |
| Firewall / Réseau | pfSense, VPN IPSec, OpenVPN, VLAN |
| Systèmes | Windows Server 2025, Ubuntu Server 22.04, Debian 12 |
| Annuaire | Active Directory, DNS, DHCP, RDP |
| Stockage collaboratif | Nextcloud, Apache, MariaDB, PHP |
| Supervision | Centreon, Nagios, SNMP |
| Sécurité | PKI (CA ATP), KeePass, règles firewall |
| Cloud (conseil) | Microsoft Azure, Azure AD |

---

## 📁 Structure du repo

```
mspr-atp-infrastructure/
├── README.md
├── docs/
│   └── rapport_final_mspr_bloc5.pdf
└── schemas/
└── schema_reseau_atp.png
```
---

## 👤 Réalisé par

Projet réalisé en équipe dans le cadre de la certification  
**Expert Informatique et Système d'Information – RNCP Niveau 7**  
EPSI | Promotion 2024-2026



--------------------------




# 🌐 MSPR ATP – Infrastructure Réseau Multi-Sites Virtualisée

> Projet réalisé dans le cadre de la certification RNCP Niveau 7 – Expert en Informatique et SI  
> Bloc 5 – Concevoir et sécuriser des solutions d'infrastructures virtualisées et cloud

---

## 📋 Contexte

L'Association of Tennis Professionals (ATP) souhaitait uniformiser son système d'information 
fragmenté entre 4 sites mondiaux (Londres, Monaco, USA, Australie) et faciliter l'accès 
sécurisé pour ses utilisateurs nomades. Notre équipe est intervenue en tant que prestataire 
informatique pour concevoir, déployer et sécuriser l'infrastructure complète.

---

## 🏗️ Architecture déployée

![Schéma réseau ATP](schemas/schema_reseau_atp.jpeg)

### Sites et adressage réseau

| Site | VLAN Admin | VLAN Users | Firewall |
|------|-----------|------------|---------|
| Londres (siège) | 192.168.1.0/24 (VLAN 11) | 192.168.10.0/24 (VLAN 10) | pfSense UK |
| Monaco (PRA) | 192.168.3.0/24 (VLAN 21) | 192.168.30.0/24 (VLAN 20) | pfSense Monaco |
| USA | 192.168.4.0/24 (VLAN 31) | 192.168.40.0/24 (VLAN 30) | pfSense US |
| Australie | 192.168.2.0/24 (VLAN 41) | 192.168.20.0/24 (VLAN 40) | pfSense AUS |
| Datacenter Cloud | 13.13.13.0/24 (VLAN 99) | — | pfSense DC |

---

## ⚙️ Missions techniques réalisées

### T1 – Infrastructure de virtualisation (VMware ESXi)
- Déploiement d'un hyperviseur VMware ESXi hébergeant 23 machines virtuelles
- Création des vSwitchs et port groups avec VLAN tagging par site
- Systèmes déployés : Windows Server 2025, Ubuntu Server, Debian, FreeBSD (pfSense)

### T2 – Active Directory & Bureaux virtuels (RDP)
- Contrôleur de domaine principal à Londres (`atp.local`)
- Réplication AD vers les USA (haute disponibilité)
- Serveur AD dormant à Monaco pour le PRA
- Service DHCP et DNS intégré et redondant
- Bureaux virtuels (RDS) accessibles via VPN pour les nomades

### T3 – Plateforme de stockage Nextcloud
- Déploiement de Nextcloud sur Ubuntu Server (Apache + MariaDB + PHP)
- Partage de fichiers via lien web, synchronisation client local
- Réplication vers le site de Monaco pour le PCA

### T4 – Firewalls pfSense & VPN IPSec inter-sites
- Un pfSense par site avec règles de filtrage par principe du moindre privilège
- Tunnels VPN IPSec site-à-site entre tous les sites et Londres
- Chiffrement AES-256, IKEv2, SHA-256, DH 2048 bits

### T5 – Accès nomades via OpenVPN
- Serveur OpenVPN sur le pfSense de Londres
- Création de CA et certificats clients (SSL/TLS + authentification utilisateur)
- Accès sécurisé au réseau interne puis RDP depuis n'importe où

### T6 – Supervision & Alerting (Centreon)
- Centreon déployé sur Debian Server à Londres
- Supervision : disponibilité des services, CPU/RAM/disque, trafic réseau (SNMP)
- Alertes e-mail configurées avec seuils personnalisés

### T7 – Plan de Reprise d'Activité (PRA)
- Site de secours : Monaco
- Serveurs dormants : AD, DNS, Nextcloud, RDS
- RPO cible : 36h | RTO cible : 48h
- Procédure de bascule documentée (tunnel VPN niveau 2 + redirection DNS)

---

## 🔍 Partie Conseil & R&D

| Mission | Contenu |
|---------|---------|
| C1 – Migration Cloud | Recommandation Microsoft Azure (lift & shift), analyse CAPEX/OPEX |
| C2 – UCaaS | Proposition Microsoft Teams + Teams Phone System |
| C3 – SSO Cloud | Étude de faisabilité Azure AD / Okta, MFA, accès conditionnel |
| C4 – Procédure PRA | Analyse de 10 risques, procédure de déclenchement étape par étape |

---

## 🛠️ Environnement technique

| Catégorie | Technologies |
|-----------|-------------|
| Virtualisation | VMware ESXi (vSphere) |
| Systèmes | Windows Server 2025, Ubuntu Server, Debian, FreeBSD |
| Sécurité | pfSense, VPN IPSec, OpenVPN, KeePass |
| Annuaire | Active Directory, DNS, DHCP, RDS |
| Stockage | Nextcloud, Apache, MariaDB |
| Supervision | Centreon (basé Nagios), SNMP |
| Cloud (étude) | Microsoft Azure, Azure AD |

---

## 📁 Structure du repo

mspr-atp-infrastructure/
├── README.md
├── docs/
│   └── rapport_final.pdf
└── schemas/
└── schema_reseau_atp.jpeg

---

## 👥 Équipe projet

Projet réalisé en équipe de 3 dans le cadre du Master 2 EPSI – spécialité Infrastructure.

---

## 📄 Licence

Projet académique – EPSI / RNCP35584
