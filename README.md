# Homelab Infrastructure

A self-hosted homelab built around Proxmox VE, TrueNAS, Linux containers, networking, storage, and private cloud services.

This repository documents the current infrastructure, network architecture, deployed services, security practices, hardware, and future expansion plans.

## 1. Project Overview

The goal of this homelab is to build a reliable, expandable, self-hosted environment for:

- Virtualization and container management
- Network-attached storage (NAS)
- Private file and photo storage
- Password management
- Secure remote access
- DNS filtering and network management
- Reverse proxy and domain management
- Media streaming
- Future hardware and network expansion

The infrastructure currently runs on an HP EliteDesk Mini using Proxmox VE as its hypervisor.

## 2. Current Hardware

| Component | Hardware | Status |
|---|---|---|
| Main server | HP EliteDesk Mini | Operational |
| CPU | Intel Core i5 | Operational |
| Memory | 16 GB RAM | Operational |
| Primary storage | 256 GB SSD | Operational |
| Hypervisor | Proxmox VE | Operational |
| Secondary computer | Raspberry Pi | Operational |
| Internet gateway | AT&T router | Operational |
| Network switch | TP-Link 5-port Gigabit switch | Operational |

### Current Hardware Architecture

```text
                 INTERNET
                    |
              AT&T ROUTER
                    |
          TP-Link 5-Port Switch
                    |
          +---------+----------+
          |                    |
     HP EliteDesk           Raspberry Pi
          |
       Proxmox VE
          |
    +-----+-------------------------+
    |              |                |
 TrueNAS VM    LXC Containers    Future VMs
               | 
          +----+------------------------------+
          |         |          |              |
       Vaultwarden WireGuard Pi-hole     Nginx Proxy
                                           Manager
```

Cloudflare provides external domain and DNS management.

## 3. Virtualization Architecture

### Proxmox VE

**Role:** Primary virtualization platform.

Proxmox runs directly on the HP EliteDesk Mini and manages the virtual machines and Linux containers.

Responsibilities:
- VM management
- LXC container management
- Virtual networking
- Storage management
- Resource allocation
- Host monitoring
- Backup management

### TrueNAS Virtual Machine

**Status:** Running; physical NAS storage not yet installed.

Purpose:
- Centralized network storage
- ZFS storage management
- SMB file sharing
- Backup storage
- Future private cloud storage

Initial storage target: **3–6 TB usable capacity**.

Planned configuration:
- Install compatible physical hard drives.
- Pass physical storage devices through to the TrueNAS VM.
- Configure ZFS storage.
- Create network shares.
- Configure automated backups.
- Establish a storage expansion strategy.

Important: TrueNAS currently has no dedicated physical NAS drives or populated storage pool.

## 4. Current Services

The following services are deployed as separate Proxmox LXC containers.

| Service | Deployment | Purpose |
|---|---|---|
| Vaultwarden | LXC | Self-hosted password manager |
| WireGuard | LXC | Encrypted remote VPN access |
| Pi-hole | LXC | Network-wide DNS filtering |
| Nginx Proxy Manager | LXC | Reverse proxy and HTTPS management |
| TrueNAS | VM | Network storage platform |

### Vaultwarden

Self-hosted password management compatible with Bitwarden clients.

Primary functions:
- Password storage
- Secure credential synchronization
- Password vault access
- Encrypted password management

### WireGuard

Provides encrypted remote access to the homelab.

Primary functions:
- VPN connectivity
- Secure remote network access
- Access to internal services
- Remote administration

### Pi-hole

Network-level DNS filtering.

Primary functions:
- DNS-based advertisement filtering
- Domain blocking
- DNS request monitoring
- Network DNS management

### Nginx Proxy Manager

Reverse proxy management for hosted applications.

Primary functions:
- Reverse proxy configuration
- Domain-based service routing
- SSL certificate management
- HTTPS access to supported applications

### Cloudflare

External domain and DNS provider.

Planned architecture responsibilities:
- Domain management
- DNS records
- DNS management
- Integration with reverse proxy infrastructure

The precise external access method and DNS configuration will be documented once finalized.

## 5. Network Architecture

### Current Network

| Component | Role |
|---|---|
| AT&T Router | Internet gateway |
| TP-Link 5-Port Gigabit Switch | Wired network distribution |
| Proxmox | Virtualization host |
| Raspberry Pi | Supporting Linux device |
| Cloudflare | External DNS and domain management |

### IP Address Documentation

Private addresses are intentionally omitted from this public blueprint.

| Device | IP Address |
|---|---|
| Proxmox | `PROXMOX_IP` |
| TrueNAS | `TRUENAS_IP` |
| Vaultwarden | `VAULTWARDEN_IP` |
| WireGuard | `WIREGUARD_IP` |
| Pi-hole | `PIHOLE_IP` |
| Nginx Proxy Manager | `NPM_IP` |
| Raspberry Pi | `RASPBERRY_PI_IP` |

Actual addresses should be stored in private configuration documentation.

## 6. Security Architecture

Security is an important part of the homelab design.

Current security components:
- WireGuard encrypted VPN access
- Vaultwarden password management
- Nginx Proxy Manager reverse proxy
- Cloudflare domain and DNS management
- Pi-hole DNS filtering

### Planned Security Improvements

- Use HTTPS for externally accessible applications.
- Restrict Proxmox administration to trusted network access.
- Avoid exposing management interfaces directly to the internet.
- Use strong authentication and unique passwords.
- Keep Proxmox, containers, and applications updated.
- Maintain offline backups of important data.
- Secure SSH access.
- Document firewall and network access rules.
- Regularly test backup restoration.

**Important:** DNS filtering, a reverse proxy, and a VPN serve different security purposes. None should be treated as a replacement for a properly configured firewall or backups.

## 7. Development Roadmap

The following projects are planned for future development.

### Phase 1 — NAS Infrastructure

**Current priority**

- [ ] Select NAS hard drives.
- [ ] Install physical storage.
- [ ] Configure TrueNAS drive passthrough.
- [ ] Create ZFS storage pool.
- [ ] Configure SMB shares.
- [ ] Configure automated backups.
- [ ] Test file recovery.
- [ ] Document storage health monitoring.

Initial target: 3–6 TB usable storage.

### Phase 2 — Private Cloud

Build a self-hosted alternative to commercial cloud photo and file services.

- [ ] Research photo management platforms.
- [ ] Configure private photo storage.
- [ ] Set up automatic photo synchronization.
- [ ] Configure remote access.
- [ ] Implement backup and recovery.
- [ ] Test mobile device integration.

### Phase 3 — Media Server

Expand the homelab's media capabilities using Navidrome.

- [ ] Configure centralized music storage.
- [ ] Connect Navidrome to NAS storage.
- [ ] Configure remote access.
- [ ] Configure media backups.
- [ ] Test streaming from external devices.

### Phase 4 — Physical Rack Expansion

Build an organized 10-inch homelab rack.

- [ ] 10-inch rack enclosure
- [ ] Rack shelving
- [ ] UPS and power management
- [ ] Network switch expansion
- [ ] Patch panel and cable management
- [ ] Rack-mounted monitoring display
- [ ] Improved airflow and cooling

## 8. Backup Strategy

The planned backup architecture will prioritize data integrity and recoverability.

Future backup responsibilities:

| Data | Backup destination |
|---|---|
| Proxmox configuration | Separate backup location |
| LXC configurations | Proxmox backups |
| TrueNAS data | Independent backup storage |
| Password manager data | Secure independent backup |
| Personal photos | NAS and separate backup |
| Media library | NAS and optional secondary copy |

A RAID or ZFS redundancy configuration is not a substitute for an independent backup.

## 9. Hardware Expansion

The system is designed to expand beyond its current HP EliteDesk Mini configuration.

Potential future hardware:
- Additional storage drives
- Dedicated NAS hardware if needed
- Larger network switch
- Rack-mounted UPS
- Additional Proxmox nodes
- Network monitoring equipment
- Additional Raspberry Pi devices

## 10. Documentation and Maintenance

This repository will serve as the central documentation location for the homelab.

Recommended documentation structure:

```text
homelab/
│
├── README.md
│
├── architecture/
│   ├── network-topology.md
│   ├── virtualization.md
│   └── storage-architecture.md
│
├── hardware/
│   ├── server-specifications.md
│   ├── networking-equipment.md
│   └── rack-layout.md
│
├── services/
│   ├── proxmox.md
│   ├── truenas.md
│   ├── vaultwarden.md
│   ├── wireguard.md
│   ├── pihole.md
│   └── nginx-proxy-manager.md
│
├── security/
│   ├── security-policy.md
│   └── backup-policy.md
│
└── roadmap/
    └── future-expansion.md
```

## 11. Project Philosophy

The goal is to develop a reliable, modular, and expandable homelab using self-hosted software and accessible hardware.

Priorities:

1. Reliability and data integrity.
2. Security and controlled remote access.
3. Documentation and reproducibility.
4. Efficient resource utilization.
5. Modular hardware and software expansion.

**Project status:** Active development.

**Primary platform:** Proxmox VE.

**Primary hardware:** HP EliteDesk Mini, Intel Core i5, 16 GB RAM, 256 GB SSD.

**Next major milestone:** Build out TrueNAS storage and establish a reliable backup infrastructure.
