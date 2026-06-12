# Modul 34 – Failover Cluster Grundlagen

## Lernziele

Nach diesem Modul kannst du:

- Erklären was ein Failover Cluster ist und warum Hochverfügbarkeit wichtig ist
- Die Begriffe Node, Quorum, Heartbeat und Failover erklären
- Die Voraussetzungen für einen Windows Failover Cluster benennen
- Das Failover Clustering Feature installieren
- Einen Cluster-Validierungstest durchführen

---

## Hintergrund: Hochverfügbarkeit – wenn ein Server nicht reicht

Stell dir vor, dein Fileserver fällt aus. 50 Mitarbeiter können nicht arbeiten, bis er repariert ist. Das kann Stunden oder sogar Tage dauern.

Ein **Failover Cluster** löst dieses Problem: Zwei (oder mehr) Server arbeiten zusammen. Fällt einer aus, übernimmt der andere **automatisch** innerhalb von Sekunden.

| Ohne Cluster | Mit Cluster |
|---|---|
| Server kaputt = Dienst offline | Server kaputt = anderer übernimmt |
| Stunden Ausfallzeit | Sekunden Ausfallzeit |
| Manueller Eingriff nötig | Automatisches Failover |

### Wichtige Begriffe

| Begriff | Erklärung |
|---------|-----------|
| **Node** | Ein Server im Cluster (mindestens 2) |
| **Cluster-Rolle** | Der Dienst, der hochverfügbar laufen soll (z.B. Fileserver) |
| **Failover** | Automatisches Umschalten auf den anderen Node |
| **Failback** | Zurückschwenken auf den ursprünglichen Node |
| **Heartbeat** | Regelmäßiger "Lebenszeichen"-Check zwischen Nodes |
| **Quorum** | Abstimmungsmechanismus: Wer entscheidet, wenn ein Node weg ist? |
| **Shared Storage** | Gemeinsamer Speicher, auf den beide Nodes zugreifen |

### Quorum – wer hat das Sagen?

Bei 2 Nodes und einem Ausfall: Woher weiß der überlebende Node, dass er übernehmen soll (und nicht der andere gerade nur ein Netzwerkproblem hat)?

**Quorum** ist die Abstimmung. Jeder Node hat eine Stimme. Zusätzlich gibt es einen "Zeugen" (Witness):

| Quorum-Typ | Wie es funktioniert |
|------------|---------------------|
| **Disk Witness** | Shared Disk hat eine Stimme |
| **File Share Witness** | Eine Dateifreigabe auf einem dritten Server hat eine Stimme |
| **Cloud Witness** | Azure Blob Storage als Zeuge |

Bei 2 Nodes + 1 File Share Witness: Fällt ein Node aus, hat der andere + Witness die Mehrheit (2 von 3) → Cluster bleibt online.

---

## Schritt für Schritt: Cluster vorbereiten

### Voraussetzungen

- Mindestens 2 Windows Server (gleiche Edition, gleich gepatcht)
- Beide in derselben Domäne
- Gemeinsames Netzwerk (idealerweise 2: eines für Traffic, eines für Heartbeat)
- Gemeinsamer Speicher (iSCSI, Fibre Channel oder Storage Spaces Direct)

### Schritt 1: Zweiten Server erstellen

Erstelle eine zweite VM `SRV-FS02` (Klon von `SRV-FS01` oder neue Installation):

| Einstellung | Wert |
|-------------|------|
| VM-Name | `SRV-FS02` |
| RAM | 4 GB |
| IP | `192.168.100.21` |
| Domäne | `training.local` beitreten |

### Schritt 2: Failover Clustering Feature installieren

Auf **beiden** Nodes:

```powershell
Install-WindowsFeature Failover-Clustering -IncludeManagementTools
```

### Schritt 3: Cluster-Validierung

Bevor du einen Cluster erstellst, prüfe ob die Voraussetzungen erfüllt sind:

```powershell
Test-Cluster -Node "SRV-FS01","SRV-FS02" -ReportName "C:\temp\ClusterValidation"
```

Der Test prüft: Netzwerk, Storage, System-Konfiguration, Hyper-V-Konfiguration.

!!! warning "Alle Tests müssen bestanden werden!"
    Microsoft supportet nur Cluster, die den Validierungstest bestehen. Behebe alle Fehler bevor du fortfährst.

---

## Challenge

!!! question "Challenge: Cluster-Validierung bestehen"
    1. Erstelle `SRV-FS02` und füge ihn zur Domäne hinzu
    2. Installiere das Failover-Clustering-Feature auf beiden Servern
    3. Führe `Test-Cluster` durch und behebe alle Warnungen/Fehler
    4. Dokumentiere das Ergebnis (wie viele Tests bestanden?)

??? success "Hinweis"
    Häufige Fehler beim Validierungstest:
    
    - **Netzwerk**: Beide Nodes brauchen IP-Konnektivität zueinander
    - **DNS**: Beide müssen sich gegenseitig per Name auflösen können
    - **Storage**: Noch kein Shared Storage? Die Storage-Tests werden dann übersprungen (ist OK für diesen Schritt)
    - **Windows Updates**: Beide müssen auf dem gleichen Patchstand sein

---

Weiter zu [Modul 35 – Cluster aufbauen](modul-35-cluster-aufbau.md) →
