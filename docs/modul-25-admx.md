# Modul 25 – Administrative Vorlagen

## Lernziele

Nach diesem Modul kannst du:

- Erklären was ADMX/ADML-Dateien sind
- Den Central Store für Administrative Vorlagen einrichten
- Zusätzliche ADMX-Vorlagen installieren (z.B. für Office, Chrome, Edge)
- Eigene GPO-Einstellungen über Administrative Vorlagen konfigurieren
- Die Dokumentation (Erklärung) in jeder Vorlage nutzen

---

## Hintergrund: Administrative Vorlagen – GPOs für alles

Die "administrativen Vorlagen" sind der mächtigste Teil der Group Policy. Sie ermöglichen hunderte von Einstellungen – von "Windows Update konfigurieren" bis "Chrome-Startseite festlegen".

### Was sind ADMX/ADML-Dateien?

| Dateityp | Inhalt | Sprache |
|----------|--------|---------|
| `.admx` | Die Einstellungsdefinition (XML) | Sprachunabhängig |
| `.adml` | Die Beschreibungstexte | Sprachspezifisch (de-DE, en-US) |

Windows Server bringt viele ADMX-Dateien mit (für Windows-Einstellungen). Für Drittanbieter-Software (Office, Chrome, Firefox) musst du zusätzliche ADMX-Dateien herunterladen.

### Central Store

Standardmäßig nutzt jeder Admin seinen lokalen ADMX-Speicher. In einer Domäne willst du aber, dass **alle Admins** die gleichen Vorlagen sehen → **Central Store**.

Der Central Store ist einfach ein Ordner im SYSVOL:

```
\\training.local\SYSVOL\training.local\Policies\PolicyDefinitions\
├── de-DE\          ← Deutsche Beschreibungen (.adml)
├── en-US\          ← Englische Beschreibungen (.adml)
├── Windows.admx    ← Windows-Einstellungen
├── inetres.admx    ← Internet Explorer
└── ...
```

---

## Schritt für Schritt: Central Store einrichten

### Schritt 1: Central Store erstellen

```powershell
# Ordner erstellen
$policyPath = "\\training.local\SYSVOL\training.local\Policies\PolicyDefinitions"
New-Item -Path $policyPath -ItemType Directory -Force
New-Item -Path "$policyPath\de-DE" -ItemType Directory -Force
New-Item -Path "$policyPath\en-US" -ItemType Directory -Force
```

### Schritt 2: Windows-Vorlagen kopieren

```powershell
# Lokale ADMX-Dateien in den Central Store kopieren
$source = "C:\Windows\PolicyDefinitions"
$dest = "\\training.local\SYSVOL\training.local\Policies\PolicyDefinitions"

Copy-Item -Path "$source\*.admx" -Destination $dest -Force
Copy-Item -Path "$source\de-DE\*.adml" -Destination "$dest\de-DE" -Force
Copy-Item -Path "$source\en-US\*.adml" -Destination "$dest\en-US" -Force
```

!!! success "Central Store aktiv!"
    Ab sofort nutzt die Gruppenrichtlinienverwaltung automatisch den Central Store. Du siehst oben in der GPO-Bearbeitung den Hinweis: "Richtliniendefinitionen (ADMX-Dateien) wurden aus dem zentralen Speicher abgerufen."

### Schritt 3: Zusätzliche ADMX-Vorlagen installieren

Für Microsoft Edge:

1. Lade die "Microsoft Edge Policy Templates" herunter (Microsoft Website)
2. Extrahiere die ADMX/ADML-Dateien
3. Kopiere sie in den Central Store:

```powershell
# Beispiel: Edge-ADMX installieren
Copy-Item "C:\temp\edge-admx\msedge.admx" -Destination $dest
Copy-Item "C:\temp\edge-admx\de-DE\msedge.adml" -Destination "$dest\de-DE"
Copy-Item "C:\temp\edge-admx\en-US\msedge.adml" -Destination "$dest\en-US"
```

### Schritt 4: Einstellungen konfigurieren

Nach dem Import findest du neue Einstellungen in der GPO:

**Beispiel – Edge-Startseite festlegen:**

1. GPO bearbeiten → **Computerkonfiguration** → **Richtlinien** → **Administrative Vorlagen** → **Microsoft Edge** → **Start, Startseite und neue Registerkarte**
2. **Startseiten-URL konfigurieren** → Aktiviert → `https://intranet.training.local`

**Beispiel – Windows Update konfigurieren:**

1. **Computerkonfiguration** → **Richtlinien** → **Administrative Vorlagen** → **Windows-Komponenten** → **Windows Update**
2. **Automatische Updates konfigurieren** → Aktiviert → "4 - Autom. herunterladen und laut Zeitplan installieren"

---

## Verfügbare ADMX-Vorlagen (Auswahl)

| Software | Download-Quelle |
|----------|----------------|
| Microsoft Office / 365 | Office Administrative Template files (ADMX) |
| Microsoft Edge | Microsoft Edge Policy Templates |
| Google Chrome | Chrome Enterprise ADMX |
| Mozilla Firefox | Firefox ADMX Templates |
| Adobe Reader | Adobe Customization Wizard |
| Zoom | Zoom ADMX Templates |

---

## Challenge

!!! question "Challenge: Central Store + Edge-Richtlinie"
    1. Richte den Central Store ein (falls noch nicht geschehen)
    2. Erstelle eine GPO `Browser – Unternehmensrichtlinie` mit folgenden Einstellungen:
       - Startseite: `https://intranet.training.local`
       - SmartScreen-Filter: Aktiviert
       - Passwort-Manager: Deaktiviert (Firmenrichtlinie: Passwörter gehören in den Passwortmanager der Firma)
    3. Verknüpfe mit der OU `Computer` und prüfe auf `CLIENT01` ob Edge die Startseite zeigt

??? success "Hinweis"
    Falls du keine Edge-ADMX-Dateien hast, kannst du auch die eingebauten Windows-Vorlagen nutzen:
    
    - **Internet Explorer** (in den Standard-ADMX enthalten): Startseite festlegen unter Computerkonfiguration → Administrative Vorlagen → Windows-Komponenten → Internet Explorer
    - Oder konfiguriere stattdessen Windows Update-Einstellungen als Übung

---

Weiter zu [Modul 26 – Aufräumen](modul-26-aufräumen.md) →
