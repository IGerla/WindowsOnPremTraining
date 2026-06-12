# Windows On-Prem Einstiegstraining

Willkommen! Dieses Training führt dich Schritt für Schritt durch den Aufbau einer **Windows-Server-Landschaft** – praxisnah, selbstgeführt und auf einem zentralen Hyper-V-Host.

Du bist Azubi, Praktikant oder angehender Admin im Microsoft-Umfeld? Hier baust du echte Server auf, richtest Active Directory ein, konfigurierst DNS und DHCP – und sammelst Hands-on-Erfahrung, die direkt im Berufsalltag hilft.

---

## Deine fertige Serverlandschaft

Am Ende aller Lernpfade hast du folgende Umgebung selbst aufgebaut:

```
┌─────────────────────────────────────────────────────────┐
│                  Zentraler Hyper-V-Host                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐             │
│  │   DC01   │  │ SRV-FS01 │  │ SRV-APP01│             │
│  │ Domain   │  │ File-    │  │ Anwen-   │             │
│  │ Controller│  │ server   │  │ dungen   │             │
│  │ DNS/DHCP │  │ DFS      │  │ IIS/Web  │             │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘             │
│       │              │              │                   │
│  ─────┴──────────────┴──────────────┴─── internes Netz │
│                                                         │
│  ┌──────────┐  ┌──────────┐                            │
│  │  CLIENT01│  │ SRV-DB01 │                            │
│  │ Windows  │  │ SQL      │                            │
│  │ 10/11    │  │ Server   │                            │
│  └──────────┘  └──────────┘                            │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## Was du in Lernpfad 1 baust

Nach Abschluss von Lernpfad 1 hast du folgendes selbst aufgebaut:

| | Was | Womit |
|---|---|---|
| 🖥️ | Einen Windows Server 2022 als VM | Hyper-V Manager |
| ⚙️ | Server mit Hostname und fester IP konfiguriert | Server Manager + PowerShell |
| 🏢 | Eine Active-Directory-Domäne eingerichtet | AD DS Rolle |
| 👥 | Benutzer, Gruppen und OUs angelegt | Active Directory Users & Computers |
| 📖 | DNS-Zonen für deine Domäne konfiguriert | DNS Manager |
| 🔄 | DHCP-Server mit automatischer IP-Vergabe | DHCP Manager |

## Was du in Lernpfad 2 baust

| | Was | Womit |
|---|---|---|
| 🔗 | Netzwerk in Subnetze segmentiert (Server + Clients) | Hyper-V Switches + VLANs |
| 🚦 | Routing zwischen Subnetzen und NAT ins Internet | RRAS |
| 🔥 | Firewall-Regeln für alle Server konfiguriert | Windows Defender Firewall + PowerShell |
| 🌐 | Einen Webserver mit eigener Website aufgesetzt | IIS |
| 🔐 | Zentrale Netzwerk-Authentifizierung eingerichtet | NPS / RADIUS |
| 🔒 | VPN-Zugang für Remote-Mitarbeiter | RRAS + NPS |

---

## Übersicht der Module

### Lernpfad 1 – Grundlagen

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 0](modul-0-orientierung.md) | Orientierung: Hyper-V Manager, Begriffe, Trainingsumgebung | 30 Min |
| [Modul 1](modul-1-vm.md) | Erste VM: Windows Server 2022 erstellen | 45 Min |
| [Modul 2](modul-2-grundconfig.md) | Grundkonfiguration: Hostname, IP, Updates | 30 Min |
| [Modul 3](modul-3-activedirectory.md) | Active Directory: Domäne und erster DC | 60 Min |
| [Modul 4](modul-4-benutzer.md) | Benutzer & Gruppen: OUs, Accounts, Sicherheitsgruppen | 45 Min |
| [Modul 5](modul-5-dns.md) | DNS: Forward/Reverse Lookup, Zonen | 45 Min |
| [Modul 6](modul-6-dhcp.md) | DHCP: Scope, Reservierungen, Optionen | 40 Min |
| [Modul 7](modul-7-aufräumen.md) | Aufräumen: Snapshots sichern, VMs stoppen | 15 Min |

### Lernpfad 2 – Netzwerk & Infrastruktur

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 8](modul-8-netzwerk.md) | Netzwerk-Design: Subnetze, VLANs, virtuelle Switches | 45 Min |
| [Modul 9](modul-9-routing.md) | Routing: Windows Server als Router, NAT | 45 Min |
| [Modul 10](modul-10-firewall.md) | Windows-Firewall: Regeln, Profile, GPO-Integration | 40 Min |
| [Modul 11](modul-11-iis.md) | Webserver IIS: Installation, Website hosten, Bindings | 45 Min |
| [Modul 12](modul-12-nps.md) | NPS & RADIUS: Zentrale Netzwerkauthentifizierung | 50 Min |
| [Modul 13](modul-13-vpn.md) | VPN: Remote-Zugriff mit RRAS einrichten | 50 Min |
| [Modul 14](modul-14-aufräumen.md) | Aufräumen: Netzwerk-Dokumentation, Snapshots | 15 Min |

---

## Voraussetzungen

- Zugang zum zentralen Hyper-V-Host (bekommst du von deinem Betreuer)
- Ein PC mit **Hyper-V Manager** installiert (oder Remote Desktop zum Host)
- Grundlegende Windows-Kenntnisse (Dateien kopieren, Programme starten)
- Keine Server-Erfahrung nötig – wir starten bei Null!

!!! tip "Los geht's!"
    Starte mit [Modul 0 – Orientierung](modul-0-orientierung.md) und lerne deine Trainingsumgebung kennen.
