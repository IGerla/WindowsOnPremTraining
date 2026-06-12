# Windows On-Prem Einstiegstraining

Selbstgeführtes Windows-Server-Training für IT-Profis – eine komplette Serverlandschaft Schritt für Schritt aufbauen.

📖 **Dokumentation:** https://igerla.github.io/WindowsOnPremTraining

## Zielgruppe

Azubis, Praktikanten und angehende Windows-Admins ohne Server-Erfahrung. Du arbeitest auf einem zentralen Hyper-V-Host und baust dir deine eigene Serverlandschaft auf.

## Lernpfade

| Lernpfad | Module | Thema |
|----------|--------|-------|
| LP 1 | 0–7 | Grundlagen (VM, Grundconfig, AD, Benutzer, DNS, DHCP) |
| LP 2 | 8–14 | Netzwerk & Infrastruktur (VLAN, Routing, Firewall, NPS/RADIUS, VPN) |
| LP 3 | 15–20 | Fileserver & Drucken (Freigaben, DFS, NTFS, Drucker, Quotas) |
| LP 4 | 21–26 | Group Policy (GPO-Grundlagen, Softwareverteilung, Sicherheitsrichtlinien) |
| LP 5 | 27–32 | Zertifikate & PKI (Enterprise CA, Zertifikate, HTTPS) |
| LP 6 | 33–38 | Hochverfügbarkeit (Failover Cluster, Storage Spaces, Hyper-V Replica) |
| LP 7 | 39–43 | Monitoring & Backup (Event Viewer, Performance Monitor, Windows Server Backup) |
| LP 8 | 44–47 | Abschlussprojekte (Komplette Serverlandschaft von Null) |

## Lernpfad 1 – Grundlagen

| Modul | Thema |
|-------|-------|
| 0 | Orientierung: Hyper-V Manager, Begriffe, Trainingsumgebung |
| 1 | Erste VM: Windows Server 2022 in Hyper-V erstellen |
| 2 | Grundkonfiguration: Hostname, IP-Adresse, Updates, Server Manager |
| 3 | Active Directory: Domäne einrichten, erster Domain Controller |
| 4 | Benutzer & Gruppen: AD-Benutzer, OUs, Sicherheitsgruppen |
| 5 | DNS: Forward/Reverse Lookup, Zonen verwalten |
| 6 | DHCP: Scope erstellen, Reservierungen, Optionen |
| 7 | Aufräumen: Snapshots sichern, VMs stoppen |

### Lernpfad 2 – Netzwerk & Infrastruktur

| Modul | Thema |
|-------|-------|
| 8 | Netzwerk-Design: Subnetze, VLANs, virtuelle Switches |
| 9 | Routing: Windows Server als Router, NAT, Internetanbindung |
| 10 | Windows-Firewall: Regeln erstellen, Profile, GPO-Integration |
| 11 | Webserver IIS: Installation, Website hosten, Bindings |
| 12 | NPS & RADIUS: Netzwerkzugriffskontrolle zentral verwalten |
| 13 | VPN: Remote-Zugriff mit RRAS einrichten |
| 14 | Aufräumen: Netzwerk-Dokumentation, Snapshots |

### Lernpfad 3 – Fileserver & Drucken

| Modul | Thema |
|-------|-------|
| 15 | Dateifreigaben: SMB-Shares erstellen und Berechtigungen setzen |
| 16 | NTFS-Berechtigungen: Vererbung, effektive Rechte, AGDLP |
| 17 | DFS: Einheitliche Pfade mit DFS-Namespaces |
| 18 | Quotas & FSRM: Speicherkontingente, Datei-Screening |
| 19 | Druckserver: Drucker zentral verwalten und per GPO verteilen |
| 20 | Aufräumen: Freigaben dokumentieren, Snapshots |

### Lernpfad 4 – Group Policy

| Modul | Thema |
|-------|-------|
| 21 | GPO-Grundlagen: Erstellen, Verknüpfen, Vererben, LSDOU |
| 22 | Desktop-Verwaltung: Laufwerke mappen, Drucker, Ordnerumleitung |
| 23 | Softwareverteilung: MSI-Pakete per GPO installieren |
| 24 | Sicherheitsrichtlinien: Passwort-Policy, Audit, AppLocker |
| 25 | Administrative Vorlagen: Central Store, ADMX/ADML |
| 26 | Aufräumen: GPO-Dokumentation, Resultant Set of Policy |

### Lernpfad 5 – Zertifikate & PKI

| Modul | Thema |
|-------|-------|
| 27 | PKI-Grundlagen: Schlüssel, Zertifikate, Vertrauensketten |
| 28 | Enterprise CA: Zertifizierungsstelle installieren |
| 29 | Zertifikate ausstellen: Vorlagen, Auto-Enrollment |
| 30 | HTTPS: Webseiten mit eigenem Zertifikat absichern |
| 31 | Zertifikat-Verwaltung: Renewal, CRL, Troubleshooting |
| 32 | Aufräumen: PKI-Dokumentation, Snapshots |

### Lernpfad 6 – Hochverfügbarkeit

| Modul | Thema |
|-------|-------|
| 33 | Storage Spaces: Speicherpools, Mirror, Parity |
| 34 | Failover Cluster: Grundlagen, Quorum, Heartbeat |
| 35 | Cluster aufbauen: iSCSI Shared Storage, File Share Witness |
| 36 | Hochverfügbarer Fileserver: Cluster-Rolle, HA-Freigaben |
| 37 | Hyper-V Replica: VMs replizieren, Disaster Recovery |
| 38 | Aufräumen: HA-Dokumentation, Snapshots |

### Lernpfad 7 – Monitoring & Backup

| Modul | Thema |
|-------|-------|
| 39 | Event Viewer: Protokolle lesen, filtern, Event-IDs |
| 40 | Performance Monitor: Baselines, Data Collector Sets |
| 41 | Windows Admin Center: Zentrale Web-Verwaltung |
| 42 | Windows Server Backup: Sicherung und Wiederherstellung |
| 43 | Aufräumen: Monitoring-Dokumentation, Snapshots |

### Lernpfad 8 – Abschlussprojekte

| Modul | Thema |
|-------|-------|
| 44 | Projekt: Neue Niederlassung (komplette Infrastruktur aufbauen) |
| 45 | Projekt: Migration (Server auf neue Hardware umziehen) |
| 46 | Projekt: Troubleshooting (Fehler in kaputten Umgebungen finden) |
| 47 | Abschluss: Dokumentation, Zertifizierungswege, Azure-Ausblick |

## Lokal starten

```bash
pip install -r requirements.txt
mkdocs serve
```

Dann im Browser: http://127.0.0.1:8000
