# Modul 18 – Quotas & FSRM

## Lernziele

Nach diesem Modul kannst du:

- Erklären was FSRM (File Server Resource Manager) ist
- Quotas (Speicherkontingente) auf Ordner anwenden
- Den Unterschied zwischen harten und weichen Quotas erklären
- Datei-Screening einrichten (bestimmte Dateitypen blockieren)
- Berichte über Speichernutzung generieren

---

## Hintergrund: FSRM – Ordnung auf dem Fileserver

Ohne Kontrolle passiert auf jedem Fileserver dasselbe: Nach ein paar Monaten ist die Festplatte voll – weil jemand seine komplette Filmsammlung im Projektordner abgelegt hat.

**FSRM (File Server Resource Manager)** gibt dir drei Werkzeuge dagegen:

| Werkzeug | Was es macht | Alltags-Vergleich |
|----------|-------------|-------------------|
| **Quotas** | Maximalen Speicherplatz pro Ordner/Benutzer festlegen | Briefkasten hat maximale Größe |
| **Datei-Screening** | Bestimmte Dateitypen blockieren (z.B. .mp3, .exe) | "Keine Haustiere erlaubt"-Schild |
| **Berichte** | Übersicht wer wie viel Speicher nutzt | Monatliche Nebenkostenabrechnung |

### Harte vs. Weiche Quotas

| Typ | Verhalten |
|-----|-----------|
| **Harte Quota** | Benutzer kann NICHT mehr speichern wenn Limit erreicht | 
| **Weiche Quota** | Benutzer wird gewarnt, kann aber weiterspeichern (nur Monitoring) |

---

## Schritt für Schritt: FSRM einrichten

### Schritt 1: FSRM installieren

```powershell
Install-WindowsFeature FS-Resource-Manager -IncludeManagementTools
```

### Schritt 2: FSRM-Konsole öffnen

```powershell
fsrm.msc
```

### Schritt 3: Quota erstellen (GUI)

1. In FSRM: **Kontingentverwaltung** → **Kontingente** → Rechtsklick → **Kontingent erstellen...**
2. Konfiguration:

| Feld | Wert |
|------|------|
| Pfad | `D:\Freigaben\Abteilungen\Vertrieb` |
| Vorlage | `200 MB Limit` (oder eigene erstellen) |
| Typ | Harte Quota |

### Schritt 4: Quota per PowerShell erstellen

```powershell
# Quota-Vorlage erstellen (1 GB, hart)
New-FsrmQuotaTemplate -Name "1GB Abteilung" `
    -Size 1GB `
    -SoftLimit:$false `
    -Threshold (New-FsrmQuotaThreshold -Percentage 85 `
        -Action (New-FsrmAction -Type Event -EventType Warning `
            -Body "Warnung: Ordner [Source Io Owner] erreicht 85% der Quota auf [Quota Path]"))

# Quota auf Ordner anwenden
New-FsrmQuota -Path "D:\Freigaben\Abteilungen\Vertrieb" `
    -Template "1GB Abteilung"

# Auto-Apply: Quota automatisch auf neue Unterordner
New-FsrmAutoQuota -Path "D:\Freigaben\Benutzer" `
    -Template "1GB Abteilung"
```

### Schritt 5: Datei-Screening einrichten

Blockiere unerwünschte Dateien (z.B. Medien-Dateien auf dem Projektlaufwerk):

```powershell
# Dateigruppe anzeigen (vordefiniert)
Get-FsrmFileGroup

# Datei-Screening erstellen: Keine Audio/Video-Dateien
New-FsrmFileScreen -Path "D:\Freigaben\Projekte" `
    -Template "Block Audio and Video Files"
```

Im GUI: **Datei-Screening-Verwaltung** → **Datei-Screenings** → **Neues Datei-Screening erstellen**

### Schritt 6: Speicherbericht generieren

```powershell
# Ad-hoc-Bericht erstellen
New-FsrmStorageReport -Name "Speichernutzung Q1" `
    -Namespace "D:\Freigaben" `
    -ReportType LargeFiles, DuplicateFiles, FilesByOwner `
    -Interactive
```

Im GUI: **Speicherberichteverwaltung** → Rechtsklick → **Berichte jetzt generieren**

!!! tip "Automatische Berichte"
    Du kannst Berichte auch zeitgesteuert generieren lassen (z.B. jeden Montag) und per E-Mail an den Admin senden.

---

## Challenge

!!! question "Challenge: Quotas für Benutzerordner"
    Erstelle unter `D:\Freigaben\Benutzer` für drei Benutzer je einen persönlichen Ordner. Wende eine Auto-Apply-Quota von 500 MB an. Teste, ob die Quota greift, indem du eine große Datei erzeugst:

    ```powershell
    # Testdatei erstellen (600 MB):
    fsutil file createnew "\\SRV-FS01\Benutzer\max\testdatei.bin" 629145600
    ```

??? success "Hinweis"
    ```powershell
    # Benutzerordner erstellen
    "max","anna","tim" | ForEach-Object {
        New-Item -Path "D:\Freigaben\Benutzer\$_" -ItemType Directory -Force
    }

    # Quota-Vorlage
    New-FsrmQuotaTemplate -Name "500MB Benutzer" -Size 500MB -SoftLimit:$false

    # Auto-Apply auf den übergeordneten Ordner
    New-FsrmAutoQuota -Path "D:\Freigaben\Benutzer" -Template "500MB Benutzer"

    # Test: Diese Datei sollte fehlschlagen (600 MB > 500 MB Quota)
    fsutil file createnew "D:\Freigaben\Benutzer\max\testdatei.bin" 629145600
    ```

---

Weiter zu [Modul 19 – Druckserver](modul-19-druckserver.md) →
