# Modul 47 – Abschluss & Ausblick

## Was du erreicht hast

Du hast in diesem Training eine komplette Windows-Server-Infrastruktur aufgebaut – von der ersten VM bis zum hochverfügbaren Cluster mit Monitoring und Backup.

### Deine Infrastruktur

```
┌─────────────────────────────────────────────────────────────────┐
│                    training.local                                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  Zentrale (192.168.100.0/24)      Niederlassung (192.168.200.0/24)│
│  ┌───────┐  ┌─────────────┐      ┌───────┐                      │
│  │ DC01  │  │ FS-Cluster  │      │ DC02  │                      │
│  │ DNS   │  │ SRV-FS01    │      │ DNS   │                      │
│  │ DHCP  │  │ SRV-FS02    │      │ DHCP  │                      │
│  │ CA    │  └─────────────┘      └───────┘                      │
│  │ iSCSI │                                                       │
│  └───────┘  ┌───────────┐        ┌──────────┐                   │
│             │ SRV-APP01 │        │ CLIENT01 │                   │
│             │ IIS/WAC   │        └──────────┘                   │
│             └───────────┘                                        │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

### Was du gelernt hast (Lernpfade 1–8)

| LP | Thema | Wichtigste Skills |
|----|-------|-------------------|
| 1 | Grundlagen | VM erstellen, Windows konfigurieren, AD aufsetzen |
| 2 | Netzwerk & Infrastruktur | VLAN, Routing, Firewall, VPN |
| 3 | Fileserver & Drucken | NTFS, Freigaben, DFS, Quotas |
| 4 | Group Policy | GPOs erstellen, Softwareverteilung, Sicherheit |
| 5 | Zertifikate & PKI | Enterprise CA, HTTPS, Zertifikate ausstellen |
| 6 | Hochverfügbarkeit | Cluster, Storage Spaces, Hyper-V Replica |
| 7 | Monitoring & Backup | Event Viewer, PerfMon, WAC, Backup/Restore |
| 8 | Abschlussprojekte | Eigenständige Planung und Umsetzung |

---

## Wie geht es weiter?

### Zertifizierungen

| Zertifizierung | Fokus | Für wen |
|----------------|-------|---------|
| **AZ-800** | Windows Server Hybrid Administrator | On-Prem + Azure Hybrid |
| **AZ-801** | Windows Server Hybrid Advanced Services | Sicherheit, HA, Migration |
| **AZ-900** | Azure Fundamentals | Einstieg in die Cloud |
| **AZ-104** | Azure Administrator | Cloud-Administration |

### Nächste Themen (Selbststudium)

| Thema | Warum wichtig |
|-------|---------------|
| **PowerShell DSC** | Server-Konfiguration als Code (Infrastructure as Code) |
| **Windows Server 2025** | Neue Features, Hot-Patching |
| **Azure Arc** | On-Prem-Server mit Azure-Tools verwalten |
| **Active Directory Hardening** | Tiering-Modell, PAWs, LAPS |
| **SCCM/Intune** | Endpoint Management im großen Stil |
| **Hyper-V in der Praxis** | Live Migration, Shielded VMs |

### Von On-Prem zu Hybrid

Die Zukunft ist **Hybrid** – On-Prem und Cloud zusammen:

```
On-Prem (was du kannst)     +     Azure (nächster Schritt)
─────────────────────────────────────────────────────────
Active Directory            →     Azure AD / Entra ID
Fileserver                  →     Azure Files + Sync
Backup                      →     Azure Backup
Monitoring                  →     Azure Monitor / Sentinel
Failover Cluster            →     Azure Site Recovery
```

---

## Feedback & Dokumentation

### Deine Dokumentation

Erstelle ein Wiki oder OneNote mit:

- Netzwerkdiagramm deiner Infrastruktur
- IP-Adressplan
- Passwort-Dokumentation (sicher aufbewahren!)
- Bekannte Probleme und Lösungen
- Backup-Dokumentation (Was, Wohin, Wann)

### Feedback zum Training

!!! info "Feedback willkommen"
    Hast du Verbesserungsvorschläge für dieses Training? Fehlt ein Thema? War etwas unklar?
    
    Erstelle ein Issue auf GitHub oder sprich deinen Trainer an.

---

!!! success "Herzlichen Glückwunsch!"
    Du hast das **Windows On-Prem Einstiegstraining** abgeschlossen! Du bist jetzt in der Lage, eine Windows-Server-Infrastruktur selbstständig aufzubauen, zu verwalten und zu überwachen. Willkommen in der Welt der Windows-Administration! 🎉
