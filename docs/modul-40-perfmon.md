# Modul 40 – Performance Monitor

## Lernziele

Nach diesem Modul kannst du:

- Den Performance Monitor (perfmon) öffnen und Leistungsindikatoren hinzufügen
- Die wichtigsten Leistungsindikatoren für CPU, RAM, Disk und Netzwerk erklären
- Data Collector Sets erstellen (automatische Datensammlung über Zeit)
- Performance-Baselines erstellen und Abweichungen erkennen
- Engpässe (Bottlenecks) identifizieren und interpretieren

---

## Hintergrund: Performance Monitor – den Server röntgen

Der Event Viewer zeigt dir **was** passiert ist. Der Performance Monitor zeigt dir **wie gut** es dem Server geht – in Echtzeit und über Zeit.

### Die vier Ressourcen

| Ressource | Leistungsindikator | Warnschwelle |
|-----------|-------------------|--------------|
| **CPU** | % Processor Time | > 85% dauerhaft |
| **RAM** | Available MBytes | < 10% des RAMs |
| **Disk** | Avg. Disk Queue Length | > 2 |
| **Netzwerk** | Bytes Total/sec | > 80% der Bandbreite |

---

## Schritt für Schritt: Performance Monitor nutzen

### Schritt 1: perfmon öffnen

```powershell
perfmon
```

### Schritt 2: Leistungsindikatoren hinzufügen

1. Klicke auf das **+** Symbol (Zähler hinzufügen)
2. Wähle aus:

| Objekt | Indikator | Was er misst |
|--------|-----------|-------------|
| Processor | % Processor Time | CPU-Auslastung |
| Memory | Available MBytes | Freier Arbeitsspeicher |
| PhysicalDisk | Avg. Disk Queue Length | Festplatten-Warteschlange |
| Network Interface | Bytes Total/sec | Netzwerk-Durchsatz |

### Schritt 3: Data Collector Set erstellen

Für eine 24-Stunden-Baseline:

```powershell
# Data Collector Set erstellen
$counters = @(
    "\Processor(_Total)\% Processor Time"
    "\Memory\Available MBytes"
    "\PhysicalDisk(_Total)\Avg. Disk Queue Length"
    "\Network Interface(*)\Bytes Total/sec"
)

New-Item "C:\PerfLogs\Baseline" -ItemType Directory -Force

logman create counter "Server-Baseline" `
    -c $counters `
    -si 30 `
    -f csv `
    -o "C:\PerfLogs\Baseline\baseline" `
    -rf 24:00:00

# Starten
logman start "Server-Baseline"
```

### Schritt 4: Baseline auswerten

```powershell
# Nach 24h die Daten importieren und auswerten
$data = Import-Csv "C:\PerfLogs\Baseline\baseline_000001.csv"

# Durchschnittliche CPU-Last
$data | Measure-Object -Property '\\*\Processor(_Total)\% Processor Time' -Average |
    Select-Object Average

# Maximum RAM-Verbrauch finden
$data | Measure-Object -Property '\\*\Memory\Available MBytes' -Minimum |
    Select-Object Minimum
```

---

## Challenge

!!! question "Challenge: Server-Gesundheitscheck"
    1. Erstelle ein Data Collector Set das CPU, RAM, Disk und Netzwerk alle 15 Sekunden für 1 Stunde sammelt
    2. Erzeuge Last auf dem Server (z.B. große Datei kopieren, viele DNS-Abfragen)
    3. Stoppe die Sammlung und analysiere die CSV-Datei
    4. Identifiziere: Welche Ressource war am stärksten belastet?

??? success "Hinweis"
    ```powershell
    # Last erzeugen (CPU):
    1..4 | ForEach-Object -Parallel {
        1..1000000 | ForEach-Object { [math]::Sqrt($_) }
    }

    # Last erzeugen (Disk):
    fsutil file createnew "C:\temp\bigfile.bin" 1073741824  # 1 GB

    # Auswertung:
    logman stop "Server-Baseline"
    $data = Import-Csv (Get-ChildItem "C:\PerfLogs\Baseline\*.csv" | Select-Object -Last 1).FullName
    $data | Select-Object -Last 10
    ```

---

Weiter zu [Modul 41 – Windows Admin Center](modul-41-wac.md) →
