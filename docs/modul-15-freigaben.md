# Modul 15 – Dateifreigaben

## Lernziele

Nach diesem Modul kannst du:

- Erklären was SMB (Server Message Block) ist und wie Dateifreigaben funktionieren
- Die Fileserver-Rolle installieren
- Ordner als Netzwerkfreigaben bereitstellen (GUI + PowerShell)
- Freigabe-Berechtigungen von NTFS-Berechtigungen unterscheiden
- Freigaben über das Netzwerk von einem Client aus verbinden
- Versteckte Freigaben (Admin-Shares) erklären

---

## Hintergrund: Warum ein Fileserver?

Stell dir vor, 20 Mitarbeiter speichern ihre Dateien auf dem eigenen PC. Was passiert, wenn:

- Jemand krank wird und ein Kollege die Datei braucht?
- Der PC kaputtgeht?
- Jemand von zu Hause arbeiten will?

Ein **Fileserver** löst diese Probleme: Alle Dateien liegen zentral auf einem Server, jeder greift darauf zu – egal von welchem PC.

| Lokale Speicherung | Fileserver |
|---|---|
| Dateien nur auf einem PC | Dateien von überall erreichbar |
| PC kaputt = Daten weg | Server hat Backup + RAID |
| Keine Zugriffskontrolle | Wer darf was? → Berechtigungen |
| Kein gemeinsamer Zugriff | Team arbeitet an gleichen Ordnern |

### SMB – das Protokoll

**SMB (Server Message Block)** ist das Protokoll, mit dem Windows Dateien über das Netzwerk bereitstellt. Wenn du `\\SRV-FS01\Daten` im Explorer eingibst, spricht dein PC per SMB mit dem Server.

| SMB-Version | Windows-Version | Besonderheit |
|-------------|----------------|--------------|
| SMB 1.0 | XP / 2003 | ⚠️ Unsicher, deaktivieren! |
| SMB 2.x | Vista / 2008 | Schneller, weniger Overhead |
| SMB 3.x | 8 / 2012+ | Verschlüsselung, Multichannel |

!!! warning "SMB 1.0 deaktivieren!"
    SMB 1.0 hat schwere Sicherheitslücken (WannaCry-Ransomware nutzte diese aus). Deaktiviere es immer:
    ```powershell
    Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol -NoRestart
    ```

---

## Schritt für Schritt: Fileserver einrichten

### Schritt 1: Fileserver-Rolle installieren

```powershell
Install-WindowsFeature FS-FileServer -IncludeManagementTools
```

### Schritt 2: Ordnerstruktur erstellen

```powershell
# Datenpartition vorbereiten (falls vorhanden: D:\, sonst C:\Daten)
$basePath = "D:\Freigaben"

# Ordnerstruktur anlegen
New-Item -Path "$basePath\Abteilungen\IT" -ItemType Directory -Force
New-Item -Path "$basePath\Abteilungen\Vertrieb" -ItemType Directory -Force
New-Item -Path "$basePath\Abteilungen\Geschäftsführung" -ItemType Directory -Force
New-Item -Path "$basePath\Projekte" -ItemType Directory -Force
New-Item -Path "$basePath\Allgemein" -ItemType Directory -Force
```

### Schritt 3: Freigabe erstellen (GUI)

1. Öffne den **Server Manager** → **Datei- und Speicherdienste** → **Freigaben**
2. Klicke auf **Aufgaben** → **Neue Freigabe...**
3. Wähle **SMB-Freigabe – Schnell**
4. Fülle aus:

| Feld | Wert |
|------|------|
| Freigabeort | `D:\Freigaben\Abteilungen` |
| Freigabename | `Abteilungen$` (mit $ = versteckt) |
| Beschreibung | `Abteilungsfreigaben` |

### Schritt 4: Freigabe erstellen (PowerShell)

```powershell
# Öffentliche Freigabe
New-SmbShare -Name "Allgemein" -Path "D:\Freigaben\Allgemein" `
    -FullAccess "Domänen-Admins" -ChangeAccess "Domänen-Benutzer" `
    -Description "Allgemeine Dokumente für alle Mitarbeiter"

# Versteckte Admin-Freigabe ($ am Ende)
New-SmbShare -Name "Projekte$" -Path "D:\Freigaben\Projekte" `
    -FullAccess "Domänen-Admins" -ChangeAccess "Projekt-Team"

# Alle Freigaben anzeigen
Get-SmbShare | Format-Table Name, Path, Description
```

### Schritt 5: Vom Client verbinden

Auf `CLIENT01`:

```powershell
# Netzlaufwerk verbinden
New-PSDrive -Name "Z" -PSProvider FileSystem -Root "\\SRV-FS01\Allgemein" -Persist

# Oder im Explorer: \\SRV-FS01\Allgemein
```

!!! tip "Tipp: Netzlaufwerke per GPO"
    In einer Produktivumgebung verbindest du Netzlaufwerke nicht manuell, sondern per Group Policy (kommt in Lernpfad 4). So bekommt jeder Benutzer automatisch die richtigen Laufwerke.

---

## Freigabe-Berechtigungen vs. NTFS-Berechtigungen

Es gibt **zwei Ebenen** von Berechtigungen – das verwirrt am Anfang:

| | Freigabe-Berechtigung | NTFS-Berechtigung |
|---|---|---|
| **Wo?** | Nur beim Zugriff über Netzwerk | Immer (lokal + Netzwerk) |
| **Granularität** | Grob (Lesen, Ändern, Vollzugriff) | Fein (Lesen, Schreiben, Ausführen, Löschen...) |
| **Empfehlung** | Auf "Jeder: Vollzugriff" setzen | Hier fein steuern! |

!!! info "Best Practice"
    Setze die Freigabe-Berechtigung großzügig (z.B. "Authentifizierte Benutzer: Ändern") und steuere den Zugriff **ausschließlich über NTFS-Berechtigungen**. So vermeidest du Verwirrung durch zwei Ebenen.

---

## Challenge

!!! question "Challenge: Abteilungsfreigaben einrichten"
    Erstelle auf `SRV-FS01` drei Freigaben:

    1. `IT` – Nur die AD-Gruppe `IT-Abteilung` darf lesen und schreiben
    2. `Vertrieb` – Nur die AD-Gruppe `Vertrieb` darf lesen und schreiben
    3. `Tausch` – Alle Domänen-Benutzer dürfen lesen und schreiben

    Teste von `CLIENT01` mit verschiedenen Benutzern, ob die Berechtigungen greifen.

??? success "Hinweis"
    ```powershell
    # Auf SRV-FS01:
    New-SmbShare -Name "IT" -Path "D:\Freigaben\Abteilungen\IT" `
        -FullAccess "Domänen-Admins" -ChangeAccess "IT-Abteilung"

    New-SmbShare -Name "Vertrieb" -Path "D:\Freigaben\Abteilungen\Vertrieb" `
        -FullAccess "Domänen-Admins" -ChangeAccess "Vertrieb"

    New-SmbShare -Name "Tausch" -Path "D:\Freigaben\Allgemein" `
        -FullAccess "Domänen-Admins" -ChangeAccess "Domänen-Benutzer"

    # Auf CLIENT01 testen:
    Test-Path "\\SRV-FS01\IT"
    Get-ChildItem "\\SRV-FS01\IT"
    ```

---

Weiter zu [Modul 16 – NTFS-Berechtigungen](modul-16-ntfs.md) →
