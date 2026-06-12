# Modul 17 – DFS (Distributed File System)

## Lernziele

Nach diesem Modul kannst du:

- Erklären was DFS ist und warum es nützlich ist
- Den Unterschied zwischen DFS-Namespaces und DFS-Replikation erklären
- Einen DFS-Namespace erstellen und Freigaben einbinden
- Ordnerziele (Folder Targets) konfigurieren
- Den Zugriff über den einheitlichen DFS-Pfad testen

---

## Hintergrund: DFS – ein Pfad für alles

Stell dir vor, deine Firma hat drei Fileserver: `SRV-FS01`, `SRV-FS02` und `SRV-FS03`. Jeder Benutzer muss sich merken, welche Datei auf welchem Server liegt:

- `\\SRV-FS01\Vertrieb`
- `\\SRV-FS02\Projekte`
- `\\SRV-FS03\Vorlagen`

Das ist unübersichtlich! **DFS-Namespaces** lösen das Problem: Alle Freigaben werden unter **einem einzigen Pfad** zusammengefasst:

```
\\training.local\dfs\           ← Ein Einstiegspunkt für alles
├── Vertrieb\                   ← zeigt auf \\SRV-FS01\Vertrieb
├── Projekte\                   ← zeigt auf \\SRV-FS02\Projekte
└── Vorlagen\                   ← zeigt auf \\SRV-FS03\Vorlagen
```

| Ohne DFS | Mit DFS |
|----------|---------|
| Jeder merkt sich Server + Freigabe | Ein Pfad: `\\training.local\dfs\...` |
| Server-Umzug = alle Links kaputt | Server-Umzug = nur DFS-Ziel ändern |
| Kein Failover | Mehrere Ziele = Ausfallsicherheit |

### DFS-Namespaces vs. DFS-Replikation

| Feature | DFS-Namespaces | DFS-Replikation (DFS-R) |
|---------|---------------|------------------------|
| Was es tut | Einheitliche Pfade | Dateien zwischen Servern synchronisieren |
| Wann nutzen | Immer (Ordnung!) | Multi-Site, Redundanz |
| Benötigt | DFS-Namespace-Rolle | DFS-Replikation-Rolle |

---

## Schritt für Schritt: DFS-Namespace einrichten

### Schritt 1: DFS-Rollen installieren

```powershell
Install-WindowsFeature FS-DFS-Namespace, FS-DFS-Replication, RSAT-DFS-Mgmt-Con
```

### Schritt 2: DFS-Namespace erstellen

1. Öffne **DFS-Verwaltung** (dfsmgmt.msc)
2. Rechtsklick auf **Namespaces** → **Neuer Namespace...**
3. Konfiguration:

| Feld | Wert |
|------|------|
| Namespace-Server | `SRV-FS01` |
| Namespace-Name | `dfs` |
| Namespace-Typ | **Domänenbasiert** (empfohlen) |

Ergebnis: Der Pfad `\\training.local\dfs` ist jetzt erreichbar.

```powershell
# Oder per PowerShell:
New-DfsnRoot -TargetPath "\\SRV-FS01\dfs" -Type DomainV2 -Path "\\training.local\dfs"
```

### Schritt 3: Ordner (Verknüpfungen) hinzufügen

1. In der DFS-Verwaltung: Rechtsklick auf den Namespace → **Neuer Ordner...**
2. Ordnername: `Vertrieb`
3. **Ordnerziel hinzufügen** → `\\SRV-FS01\Vertrieb`
4. Wiederholen für weitere Freigaben

```powershell
# Per PowerShell:
New-DfsnFolder -Path "\\training.local\dfs\Vertrieb" `
    -TargetPath "\\SRV-FS01\Vertrieb"

New-DfsnFolder -Path "\\training.local\dfs\Projekte" `
    -TargetPath "\\SRV-FS01\Projekte"

New-DfsnFolder -Path "\\training.local\dfs\Allgemein" `
    -TargetPath "\\SRV-FS01\Allgemein"
```

### Schritt 4: Vom Client testen

```powershell
# DFS-Pfad aufrufen
Get-ChildItem "\\training.local\dfs"

# Netzlaufwerk auf DFS-Pfad mappen
New-PSDrive -Name "S" -PSProvider FileSystem -Root "\\training.local\dfs" -Persist
```

!!! success "Geschafft!"
    Alle Benutzer können jetzt über `\\training.local\dfs\` auf sämtliche Freigaben zugreifen – egal auf welchem physischen Server die Daten liegen.

!!! tip "Tipp: Netzlaufwerk per GPO"
    In der Praxis mappst du `\\training.local\dfs` als Netzlaufwerk per Group Policy. So sieht jeder Benutzer im Explorer automatisch Laufwerk `S:\` mit allen Abteilungsordnern.

---

## Challenge

!!! question "Challenge: DFS-Namespace mit mehreren Ordnern"
    Erstelle einen DFS-Namespace `\\training.local\firma` mit folgenden Ordnern:

    - `Vorlagen` → zeigt auf `\\SRV-FS01\Vorlagen`
    - `IT-Intern` → zeigt auf `\\SRV-FS01\IT`
    - `Tausch` → zeigt auf `\\SRV-FS01\Tausch`

    Verbinde dich von `CLIENT01` und prüfe ob alle Ordner unter `\\training.local\firma\` erreichbar sind.

??? success "Hinweis"
    ```powershell
    # Namespace erstellen
    New-DfsnRoot -TargetPath "\\SRV-FS01\firma" -Type DomainV2 -Path "\\training.local\firma"

    # Ordner hinzufügen
    New-DfsnFolder -Path "\\training.local\firma\Vorlagen" -TargetPath "\\SRV-FS01\Vorlagen"
    New-DfsnFolder -Path "\\training.local\firma\IT-Intern" -TargetPath "\\SRV-FS01\IT"
    New-DfsnFolder -Path "\\training.local\firma\Tausch" -TargetPath "\\SRV-FS01\Tausch"

    # Auf CLIENT01 testen:
    Test-Path "\\training.local\firma\Vorlagen"
    Get-ChildItem "\\training.local\firma"
    ```

---

Weiter zu [Modul 18 – Quotas & FSRM](modul-18-fsrm.md) →
