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

## Was du in Lernpfad 3 baust

| | Was | Womit |
|---|---|---|
| 📁 | Zentrale Dateifreigaben mit Zugriffskontrolle | SMB + Freigabe-Berechtigungen |
| 🔑 | Fein granulare Dateiberechtigungen nach AGDLP | NTFS-Berechtigungen |
| 🗂️ | Einheitliche Pfade für alle Freigaben | DFS-Namespaces |
| 📊 | Speicherkontingente und Dateitypfilter | FSRM (Quotas + Screening) |
| 🖨️ | Zentrale Druckerverwaltung mit automatischer Verteilung | Druckserver + GPO |

## Was du in Lernpfad 4 baust

| | Was | Womit |
|---|---|---|
| 📋 | GPOs erstellen, verknüpfen und die Verarbeitungsreihenfolge steuern | Gruppenrichtlinienverwaltung |
| 🖥️ | Arbeitsplätze automatisch einrichten (Laufwerke, Drucker, Ordner) | GPO Preferences |
| 📦 | Software automatisch auf Clients installieren | GPO-Softwareverteilung (MSI) |
| 🔒 | Sicherheitsrichtlinien zentral erzwingen | Audit, Lockout, AppLocker |
| 📄 | Hunderte Einstellungen über ADMX-Vorlagen konfigurieren | Central Store |

## Was du in Lernpfad 5 baust

| | Was | Womit |
|---|---|---|
| 🔐 | PKI-Konzepte verstehen (Schlüssel, Zertifikate, Vertrauensketten) | Theorie + certutil |
| 🏢 | Eigene Zertifizierungsstelle betreiben | AD CS (Enterprise CA) |
| 📨 | Zertifikate automatisch an alle Computer verteilen | Auto-Enrollment + GPO |
| 🌐 | Interne Webseiten per HTTPS absichern | IIS + eigene Zertifikate |
| 🛡️ | Zertifikate sperren und Lebenszyklus verwalten | CRL, Renewal, Templates |

## Was du in Lernpfad 6 baust

| | Was | Womit |
|---|---|---|
| 💾 | Festplatten zu ausfallsicheren Storage Pools zusammenfassen | Storage Spaces |
| 🔄 | Einen Zwei-Knoten-Failover-Cluster aufbauen | Failover Clustering |
| 🗄️ | Shared Storage über das Netzwerk bereitstellen | iSCSI |
| 📂 | Einen hochverfügbaren Fileserver betreiben | Cluster-Fileserver-Rolle |
| 🌐 | VMs für Disaster Recovery auf zweiten Host replizieren | Hyper-V Replica |

## Was du in Lernpfad 7 baust

| | Was | Womit |
|---|---|---|
| 📋 | Ereignisprotokolle effektiv auswerten und filtern | Event Viewer + PowerShell |
| 📊 | Leistungsdaten sammeln und Engpässe erkennen | Performance Monitor |
| 🖥️ | Alle Server zentral über den Browser verwalten | Windows Admin Center |
| 💾 | Automatische Backups mit gestestetem Restore | Windows Server Backup |

## Was du in Lernpfad 8 baust

| | Was | Womit |
|---|---|---|
| 🏗️ | Komplette Infrastruktur für einen neuen Standort planen und aufbauen | Alles aus LP 1–7 |
| 🚚 | Einen bestehenden Server auf neue Hardware migrieren | Robocopy, IP-Tausch |
| 🔍 | Fehler in einer kaputten Umgebung systematisch finden und beheben | Troubleshooting-Tools |
| 🎓 | Nächste Schritte: Zertifizierungen und Azure-Ausblick | Dokumentation |

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

### Lernpfad 3 – Fileserver & Drucken

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 15](modul-15-freigaben.md) | Dateifreigaben: SMB-Shares, Berechtigungen | 50 Min |
| [Modul 16](modul-16-ntfs.md) | NTFS-Berechtigungen: Vererbung, AGDLP | 50 Min |
| [Modul 17](modul-17-dfs.md) | DFS: Einheitliche Pfade, Namespaces | 40 Min |
| [Modul 18](modul-18-fsrm.md) | Quotas & FSRM: Speicherkontingente, Datei-Screening | 40 Min |
| [Modul 19](modul-19-druckserver.md) | Druckserver: Drucker verwalten, GPO-Verteilung | 45 Min |
| [Modul 20](modul-20-aufräumen.md) | Aufräumen: Freigaben dokumentieren, Snapshots | 15 Min |

### Lernpfad 4 – Group Policy

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 21](modul-21-gpo.md) | GPO-Grundlagen: Erstellen, Verknüpfen, LSDOU | 50 Min |
| [Modul 22](modul-22-desktop.md) | Desktop-Verwaltung: Laufwerke, Drucker, Ordnerumleitung | 50 Min |
| [Modul 23](modul-23-software.md) | Softwareverteilung: MSI-Pakete per GPO | 45 Min |
| [Modul 24](modul-24-sicherheit.md) | Sicherheitsrichtlinien: Passwort, Audit, AppLocker | 50 Min |
| [Modul 25](modul-25-admx.md) | Administrative Vorlagen: Central Store, ADMX | 40 Min |
| [Modul 26](modul-26-aufräumen.md) | Aufräumen: GPO-Dokumentation, RSoP | 15 Min |

### Lernpfad 5 – Zertifikate & PKI

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 27](modul-27-pki.md) | PKI-Grundlagen: Schlüssel, Zertifikate, Vertrauensketten | 45 Min |
| [Modul 28](modul-28-ca.md) | Enterprise CA: Zertifizierungsstelle installieren | 50 Min |
| [Modul 29](modul-29-zertifikate.md) | Zertifikate ausstellen: Vorlagen, Auto-Enrollment | 50 Min |
| [Modul 30](modul-30-https.md) | HTTPS: Webseiten mit eigenem Zertifikat absichern | 40 Min |
| [Modul 31](modul-31-verwaltung.md) | Zertifikat-Verwaltung: Renewal, CRL, Troubleshooting | 45 Min |
| [Modul 32](modul-32-aufräumen.md) | Aufräumen: PKI-Dokumentation, Snapshots | 15 Min |

### Lernpfad 6 – Hochverfügbarkeit

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 33](modul-33-storage.md) | Storage Spaces: Pools, Mirror, Parity | 50 Min |
| [Modul 34](modul-34-cluster.md) | Failover Cluster: Grundlagen, Quorum, Heartbeat | 45 Min |
| [Modul 35](modul-35-cluster-aufbau.md) | Cluster aufbauen: iSCSI, File Share Witness | 60 Min |
| [Modul 36](modul-36-ha-fileserver.md) | Hochverfügbarer Fileserver: Cluster-Rolle | 45 Min |
| [Modul 37](modul-37-replica.md) | Hyper-V Replica: Disaster Recovery | 50 Min |
| [Modul 38](modul-38-aufräumen.md) | Aufräumen: HA-Dokumentation, Snapshots | 15 Min |

### Lernpfad 7 – Monitoring & Backup

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 39](modul-39-eventviewer.md) | Event Viewer: Protokolle, Filter, Event-IDs | 45 Min |
| [Modul 40](modul-40-perfmon.md) | Performance Monitor: Baselines, Data Collectors | 45 Min |
| [Modul 41](modul-41-wac.md) | Windows Admin Center: Zentrale Web-Verwaltung | 40 Min |
| [Modul 42](modul-42-backup.md) | Windows Server Backup: Sicherung und Restore | 50 Min |
| [Modul 43](modul-43-aufräumen.md) | Aufräumen: Monitoring-Dokumentation, Snapshots | 15 Min |

### Lernpfad 8 – Abschlussprojekte

| Modul | Thema | Dauer |
|-------|-------|-------|
| [Modul 44](modul-44-projekt-niederlassung.md) | Projekt: Neue Niederlassung aufbauen | 120 Min |
| [Modul 45](modul-45-projekt-migration.md) | Projekt: Server-Migration durchführen | 90 Min |
| [Modul 46](modul-46-projekt-troubleshooting.md) | Projekt: Troubleshooting – Fehler finden | 90 Min |
| [Modul 47](modul-47-abschluss.md) | Abschluss: Dokumentation, Zertifizierungen, Ausblick | 30 Min |

---

## Voraussetzungen

- Zugang zum zentralen Hyper-V-Host (bekommst du von deinem Betreuer)
- Ein PC mit **Hyper-V Manager** installiert (oder Remote Desktop zum Host)
- Grundlegende Windows-Kenntnisse (Dateien kopieren, Programme starten)
- Keine Server-Erfahrung nötig – wir starten bei Null!

!!! tip "Los geht's!"
    Starte mit [Modul 0 – Orientierung](modul-0-orientierung.md) und lerne deine Trainingsumgebung kennen.
