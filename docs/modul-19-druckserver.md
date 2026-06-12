# Modul 19 – Druckserver

## Lernziele

Nach diesem Modul kannst du:

- Die Druckserver-Rolle installieren und konfigurieren
- Drucker hinzufügen und Treiber verwalten
- Drucker im Active Directory veröffentlichen
- Drucker per GPO automatisch auf Clients verteilen
- Druckerwarteschlangen überwachen und verwalten
- Druckberechtigungen setzen (wer darf wo drucken?)

---

## Hintergrund: Warum ein Druckserver?

Ohne Druckserver muss jeder PC seinen eigenen Druckertreiber installieren und direkt mit dem Drucker kommunizieren. Bei 50 PCs und 5 Druckern heißt das: 50 × manuell installieren.

Ein **Druckserver** zentralisiert alles:

| Ohne Druckserver | Mit Druckserver |
|---|---|
| Treiber auf jedem PC installieren | Treiber nur einmal auf dem Server |
| Drucker manuell einrichten | Drucker per GPO automatisch verteilen |
| Kein Überblick über Druckaufträge | Zentrale Warteschlange und Logging |
| Drucker-Umzug = überall neu | Drucker-Umzug = nur Server ändern |

---

## Schritt für Schritt: Druckserver einrichten

### Schritt 1: Rolle installieren

```powershell
Install-WindowsFeature Print-Server -IncludeManagementTools
```

### Schritt 2: Drucker hinzufügen

Da wir in der Trainingsumgebung keine physischen Drucker haben, verwenden wir einen **virtuellen Drucker** (Microsoft Print to PDF oder einen simulierten Netzwerkdrucker):

```powershell
# Port erstellen (simuliert einen Netzwerkdrucker)
Add-PrinterPort -Name "IP_10.0.0.100" -PrinterHostAddress "10.0.0.100"

# Drucker hinzufügen (mit generischem Treiber)
Add-Printer -Name "Drucker-EG-Flur" `
    -DriverName "Microsoft Print To PDF" `
    -PortName "IP_10.0.0.100" `
    -Location "Erdgeschoss, Flur" `
    -Comment "Farblaser für alle Mitarbeiter"
```

Im GUI: **Druckverwaltung** (printmanagement.msc) → Server → Rechtsklick **Drucker** → **Drucker hinzufügen**

### Schritt 3: Drucker im AD veröffentlichen

Damit Benutzer den Drucker über die Active-Directory-Suche finden können:

```powershell
# Drucker in AD veröffentlichen
Set-Printer -Name "Drucker-EG-Flur" -Published $true
```

Im GUI: Rechtsklick auf den Drucker → **In Verzeichnis auflisten**

### Schritt 4: Drucker per GPO verteilen

1. Öffne **Gruppenrichtlinienverwaltung** auf dem DC
2. Erstelle eine neue GPO: `Drucker – Erdgeschoss`
3. Bearbeiten → **Benutzerkonfiguration** → **Einstellungen** → **Systemsteuerung** → **Drucker**
4. Rechtsklick → **Neu** → **Freigegebener Drucker**
5. Freigabepfad: `\\SRV-FS01\Drucker-EG-Flur`
6. Optional: **Als Standarddrucker festlegen**
7. GPO mit der passenden OU verknüpfen

### Schritt 5: Druckberechtigungen setzen

```powershell
# Wer darf drucken?
# Standard: Jeder darf drucken
# Einschränkung: Nur bestimmte Gruppe
Set-Printer -Name "Drucker-EG-Flur" -PermissionSDDL (
    (Get-Printer -Name "Drucker-EG-Flur").PermissionSDDL
)

# Im GUI einfacher:
# Druckverwaltung → Drucker → Rechtsklick → Eigenschaften → Sicherheit
```

### Schritt 6: Warteschlange überwachen

```powershell
# Alle Druckaufträge anzeigen
Get-PrintJob -PrinterName "Drucker-EG-Flur"

# Warteschlange leeren
Get-PrintJob -PrinterName "Drucker-EG-Flur" | Remove-PrintJob

# Drucker-Status prüfen
Get-Printer | Format-Table Name, PrinterStatus, JobCount
```

---

## Challenge

!!! question "Challenge: Abteilungsdrucker einrichten"
    Erstelle zwei Drucker auf `SRV-FS01`:

    1. `Drucker-IT` – Standort: "2. OG, IT-Büro", nur Gruppe `IT-Abteilung` darf drucken
    2. `Drucker-Allgemein` – Standort: "EG, Empfang", alle Domänen-Benutzer dürfen drucken

    Veröffentliche beide im AD und erstelle eine GPO, die `Drucker-Allgemein` automatisch auf allen Clients als Standarddrucker installiert.

??? success "Hinweis"
    ```powershell
    # Ports erstellen
    Add-PrinterPort -Name "IP_10.0.0.101" -PrinterHostAddress "10.0.0.101"
    Add-PrinterPort -Name "IP_10.0.0.102" -PrinterHostAddress "10.0.0.102"

    # Drucker erstellen
    Add-Printer -Name "Drucker-IT" -DriverName "Microsoft Print To PDF" `
        -PortName "IP_10.0.0.101" -Location "2. OG, IT-Büro" -Published $true

    Add-Printer -Name "Drucker-Allgemein" -DriverName "Microsoft Print To PDF" `
        -PortName "IP_10.0.0.102" -Location "EG, Empfang" -Published $true

    # Für die GPO-Verteilung:
    # Gruppenrichtlinienverwaltung → Neue GPO → Drucker hinzufügen
    # Freigabepfad: \\SRV-FS01\Drucker-Allgemein
    # Haken: "Als Standarddrucker festlegen"
    ```

---

Weiter zu [Modul 20 – Aufräumen](modul-20-aufräumen.md) →
