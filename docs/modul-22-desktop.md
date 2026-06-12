# Modul 22 – Desktop-Verwaltung per GPO

## Lernziele

Nach diesem Modul kannst du:

- Netzlaufwerke per GPO automatisch verbinden (Drive Maps)
- Drucker per GPO zuweisen
- Startmenü und Taskleiste per GPO konfigurieren
- Ordnerumleitungen einrichten (Desktop, Dokumente auf Server)
- Logon-Skripte per GPO verteilen
- Item-Level Targeting für zielgenaue GPO-Anwendung nutzen

---

## Hintergrund: Der perfekte Arbeitsplatz – automatisch

Stell dir vor, ein neuer Mitarbeiter im Vertrieb startet: Er meldet sich an und hat sofort:

- Laufwerk `V:\` mit den Vertriebsdaten
- Den Flur-Drucker als Standarddrucker
- Seine Dokumente auf dem Server gespeichert (nicht lokal)
- Die Firmen-Verknüpfungen im Startmenü

All das kannst du mit GPOs automatisieren. Der Benutzer muss **nichts** manuell einrichten.

---

## Schritt für Schritt: Netzlaufwerke per GPO

### Schritt 1: GPO erstellen und bearbeiten

1. Erstelle eine GPO: `Laufwerke – Vertrieb`
2. Bearbeiten → **Benutzerkonfiguration** → **Einstellungen** → **Windows-Einstellungen** → **Laufwerkszuordnungen**
3. Rechtsklick → **Neu** → **Zugeordnetes Laufwerk**

| Feld | Wert |
|------|------|
| Aktion | Erstellen |
| Speicherort | `\\training.local\dfs\Vertrieb` |
| Laufwerkbuchstabe | `V:` |
| Bezeichnung | `Vertrieb` |
| Verbindung wiederherstellen | ✅ |

### Schritt 2: Item-Level Targeting

Damit das Laufwerk **nur** für Vertriebs-Mitarbeiter gilt:

1. Im Laufwerkseintrag: Reiter **Gemeinsam** → **Zielgruppenadressierung auf Elementebene** aktivieren
2. **Zielgruppenadressierung...** klicken
3. **Neues Element** → **Sicherheitsgruppe** → `GG_Vertrieb`

So bekommt nur die Vertriebs-Gruppe das Laufwerk – auch wenn die GPO auf einer breiteren OU liegt.

### Schritt 3: Weitere Laufwerke hinzufügen

```
V: → \\training.local\dfs\Vertrieb     (nur Vertrieb)
P: → \\training.local\dfs\Projekte     (nur Projekt-Team)
T: → \\training.local\dfs\Tausch       (alle)
H: → \\training.local\dfs\Benutzer\%USERNAME%  (persönlich)
```

!!! tip "Tipp: %USERNAME%"
    Die Variable `%USERNAME%` wird automatisch durch den Anmeldenamen ersetzt. Jeder Benutzer bekommt so sein eigenes Home-Laufwerk.

---

## Ordnerumleitung (Folder Redirection)

Standardmäßig liegen Desktop und Dokumente lokal auf dem PC. Bei einer Ordnerumleitung werden sie auf den Server umgeleitet – der Benutzer merkt davon nichts.

### Konfiguration

1. GPO bearbeiten → **Benutzerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Ordnerumleitung**
2. Rechtsklick auf **Dokumente** → **Eigenschaften**
3. Einstellung: **Standard – Alle Ordner an den gleichen Pfad umleiten**
4. Stammordner: `\\SRV-FS01\Benutzer\%USERNAME%\Dokumente`

| Ordner | Zielpfad |
|--------|----------|
| Dokumente | `\\SRV-FS01\Benutzer\%USERNAME%\Dokumente` |
| Desktop | `\\SRV-FS01\Benutzer\%USERNAME%\Desktop` |

!!! info "Vorteil der Ordnerumleitung"
    - Dateien liegen auf dem Server → Backup inklusive
    - Benutzer kann sich an jedem PC anmelden und hat seine Dateien
    - Offline-Dateien ermöglichen Zugriff ohne Netzwerk

---

## Logon-Skripte

Für Aktionen, die GPO-Einstellungen nicht abdecken, gibt es Logon-Skripte:

1. GPO bearbeiten → **Benutzerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Skripts (Anmelden/Abmelden)**
2. Doppelklick auf **Anmelden** → Reiter **PowerShell-Skripts**
3. **Hinzufügen** → Skript auswählen

Skripte sollten im SYSVOL-Ordner liegen (wird automatisch auf alle DCs repliziert):

```
\\training.local\SYSVOL\training.local\scripts\logon.ps1
```

Beispiel-Logon-Skript:

```powershell
# logon.ps1 – Wird bei jeder Anmeldung ausgeführt
Write-EventLog -LogName Application -Source "Logon-Script" `
    -EventId 1000 -Message "Benutzer $env:USERNAME hat sich angemeldet"
```

---

## Challenge

!!! question "Challenge: Kompletter Arbeitsplatz per GPO"
    Erstelle eine GPO `Arbeitsplatz – Standardbenutzer` die folgendes macht:

    1. Laufwerk `T:` → `\\training.local\dfs\Tausch` (für alle)
    2. Laufwerk `H:` → `\\training.local\dfs\Benutzer\%USERNAME%` (persönlich)
    3. Ordnerumleitung: Dokumente → `\\SRV-FS01\Benutzer\%USERNAME%\Dokumente`
    4. Drucker: `\\SRV-FS01\Drucker-Allgemein` als Standarddrucker

    Verknüpfe die GPO mit der OU `Benutzer`. Teste mit einem Benutzer-Account ob nach `gpupdate /force` + Neuanmeldung alles da ist.

??? success "Hinweis"
    - Laufwerke: Benutzerkonfiguration → Einstellungen → Windows-Einstellungen → Laufwerkszuordnungen
    - Ordnerumleitung: Benutzerkonfiguration → Richtlinien → Windows-Einstellungen → Ordnerumleitung
    - Drucker: Benutzerkonfiguration → Einstellungen → Systemsteuerung → Drucker → Freigegebener Drucker
    
    Prüfe mit `gpresult /r` ob die GPO unter "Angewendete Gruppenrichtlinienobjekte" erscheint.

---

Weiter zu [Modul 23 – Softwareverteilung](modul-23-software.md) →
