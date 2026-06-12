# Roadmap – Windows On-Prem Einstiegstraining

Diese Roadmap zeigt den geplanten Aufbau aller Lernpfade. Module mit ✅ sind fertig, Module mit 📝 haben Lernziele und Hintergrund, Module mit 📋 sind geplant.

---

## Lernpfad 1 – Grundlagen

**Ziel:** Eine erste Windows-Server-VM aufsetzen, Active Directory einrichten und die grundlegenden Netzwerkdienste (DNS, DHCP) konfigurieren.

| Modul | Thema | Status |
|-------|-------|--------|
| 0 | Orientierung: Hyper-V Manager, Begriffe, Trainingsumgebung | ✅ |
| 1 | Erste VM: Windows Server 2022 in Hyper-V erstellen | 📝 |
| 2 | Grundkonfiguration: Hostname, IP-Adresse, Updates | 📝 |
| 3 | Active Directory: Domäne und erster Domain Controller | 📝 |
| 4 | Benutzer & Gruppen: OUs, Accounts, Sicherheitsgruppen | 📝 |
| 5 | DNS: Forward/Reverse Lookup, Zonen verwalten | 📝 |
| 6 | DHCP: Scope, Reservierungen, Optionen | 📝 |
| 7 | Aufräumen: Snapshots sichern, VMs stoppen | 📝 |

**Ergebnis:** `DC01` als Domain Controller mit DNS und DHCP, `CLIENT01` als Domänen-Mitglied.

---

## Lernpfad 2 – Netzwerk & Infrastruktur

**Ziel:** Das Netzwerk professionell segmentieren, absichern und Remotezugriff ermöglichen.

| Modul | Thema | Status |
|-------|-------|--------|
| 8 | Netzwerk-Design: Subnetze, VLANs, virtuelle Switches | 📝 |
| 9 | Routing: Windows Server als Router, NAT, Internetanbindung | 📝 |
| 10 | Windows-Firewall: Regeln erstellen, Profile, GPO-Integration | 📝 |
| 11 | Webserver IIS: Installation, Website hosten, Bindings | 📝 |
| 12 | NPS & RADIUS: Netzwerkzugriffskontrolle zentral verwalten | 📝 |
| 13 | VPN: Remote-Zugriff mit RRAS einrichten | 📝 |
| 14 | Aufräumen: Snapshots, Netzwerk-Dokumentation | 📝 |

**Ergebnis:** Segmentiertes Netzwerk mit Routing, Firewall-Regeln, IIS-Webserver, RADIUS-Authentifizierung und VPN-Zugang.

---

## Lernpfad 3 – Fileserver & Drucken

**Ziel:** Zentrale Dateiablage mit Berechtigungen, DFS-Replikation und Druckerverwaltung.

| Modul | Thema | Status |
|-------|-------|--------|
| 15 | Dateifreigaben: SMB-Shares erstellen und Berechtigungen setzen | � |
| 16 | NTFS-Berechtigungen: Vererbung, effektive Rechte, Best Practices | 📝 |
| 17 | DFS: Distributed File System für einheitliche Pfade | 📝 |
| 18 | Quotas & FSRM: Speicherplatz begrenzen, Datei-Screening | 📝 |
| 19 | Druckserver: Drucker zentral verwalten und per GPO verteilen | 📝 |
| 20 | Aufräumen: Freigaben dokumentieren, Snapshots | 📝 |

**Ergebnis:** `SRV-FS01` als Fileserver mit DFS, NTFS-Berechtigungen, Quotas und zentraler Druckerverwaltung.

---

## Lernpfad 4 – Group Policy

**Ziel:** Unternehmensweite Konfiguration und Sicherheitsrichtlinien zentral verwalten.

| Modul | Thema | Status |
|-------|-------|--------|
| 21 | GPO-Grundlagen: Erstellen, Verknüpfen, Vererben, Filtern | 📋 |
| 22 | Desktop-Verwaltung: Startmenü, Wallpaper, Laufwerke mappen | 📋 |
| 23 | Softwareverteilung: MSI-Pakete per GPO installieren | 📋 |
| 24 | Sicherheitsrichtlinien: Passwort-Policy, Account Lockout, Audit | 📋 |
| 25 | Administrative Vorlagen: ADMX/ADML, Central Store | 📋 |
| 26 | Aufräumen: GPO-Dokumentation, Resultant Set of Policy | 📋 |

**Ergebnis:** Zentrale Verwaltung aller Clients und Server über Group Policy Objects.

---

## Lernpfad 5 – Zertifikate & PKI

**Ziel:** Eine eigene Zertifizierungsstelle betreiben und HTTPS, Smartcard-Login und sichere Kommunikation ermöglichen.

| Modul | Thema | Status |
|-------|-------|--------|
| 27 | PKI-Grundlagen: Zertifikate, Schlüsselpaare, Vertrauensketten | 📋 |
| 28 | Enterprise CA: Stammzertifizierungsstelle installieren | 📋 |
| 29 | Zertifikate ausstellen: Web-Enrollment, Auto-Enrollment | 📋 |
| 30 | HTTPS: IIS-Website mit eigenem Zertifikat absichern | 📋 |
| 31 | Zertifikat-Verwaltung: Sperrlisten (CRL), Renewal, Templates | 📋 |
| 32 | Aufräumen: CA dokumentieren, Snapshots | 📋 |

**Ergebnis:** Eigene Enterprise CA, automatische Zertifikatverteilung, HTTPS auf allen internen Webseiten.

---

## Lernpfad 6 – Hochverfügbarkeit

**Ziel:** Ausfallsichere Server-Infrastruktur mit Clustering und Replikation.

| Modul | Thema | Status |
|-------|-------|--------|
| 33 | Storage Spaces: Speicherpools und virtuelle Festplatten | 📋 |
| 34 | Failover Cluster: Grundlagen, Quorum, Cluster-Rollen | 📋 |
| 35 | Cluster aufbauen: Zwei-Knoten-Cluster mit File Share Witness | 📋 |
| 36 | Cluster-Ressourcen: Hochverfügbarer Fileserver | 📋 |
| 37 | Hyper-V Replica: VMs zwischen Hosts replizieren | 📋 |
| 38 | Aufräumen: Cluster-Dokumentation, Snapshots | 📋 |

**Ergebnis:** Zwei-Knoten-Failover-Cluster mit hochverfügbarem Fileserver und Hyper-V Replica.

---

## Lernpfad 7 – Monitoring & Backup

**Ziel:** Probleme frühzeitig erkennen, Leistung überwachen und Daten sichern.

| Modul | Thema | Status |
|-------|-------|--------|
| 39 | Event Viewer: Ereignisprotokolle lesen und filtern | 📋 |
| 40 | Performance Monitor: Leistungsindikatoren, Datencollectors | 📋 |
| 41 | Windows Admin Center: Moderne Weboberfläche für Server | 📋 |
| 42 | Windows Server Backup: Sicherung und Wiederherstellung | 📋 |
| 43 | Aufräumen: Monitoring-Dokumentation, Snapshots | 📋 |

**Ergebnis:** Zentrales Monitoring mit Alarmen, Performance-Baselines und getestete Backup/Restore-Prozedur.

---

## Lernpfad 8 – Abschlussprojekte

**Ziel:** Alles Gelernte in realistischen Szenarien anwenden.

| Modul | Thema | Status |
|-------|-------|--------|
| 44 | Projekt: Neue Niederlassung (komplette Serverlandschaft aufbauen) | 📋 |
| 45 | Projekt: Migration (Bestehende Umgebung erweitern und migrieren) | 📋 |
| 46 | Projekt: Troubleshooting (Fehler in einer kaputten Umgebung finden) | 📋 |
| 47 | Abschluss: Dokumentation, Zertifizierungswege, Ausblick auf Azure | 📋 |

**Ergebnis:** Eigenständig geplante und umgesetzte Serverlandschaften, Troubleshooting-Kompetenz.

---

## Legende

| Symbol | Bedeutung |
|--------|-----------|
| ✅ | Modul komplett ausgearbeitet |
| 📝 | Lernziele und Hintergrund vorhanden (Platzhalter) |
| 📋 | Geplant, noch nicht angelegt |
