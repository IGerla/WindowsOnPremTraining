# Modul 42 – Windows Server Backup

## Lernziele

Nach diesem Modul kannst du:

- Die Rolle "Windows Server Backup" installieren
- Einen Backup-Plan erstellen (vollständig, inkrementell)
- System State Backup durchführen (Active Directory sichern!)
- Bare-Metal-Recovery erklären (kompletter Server wiederherstellen)
- Einzelne Dateien und Ordner aus einem Backup wiederherstellen
- Backup-Jobs per PowerShell automatisieren

---

## Hintergrund: Backup – dein Rettungsring

Hochverfügbarkeit schützt gegen Hardware-Ausfälle. Aber was schützt gegen:

- Versehentliches Löschen wichtiger Dateien?
- Ransomware, die alles verschlüsselt?
- Fehlkonfiguration, die AD zerstört?
- Korrupte Datenbanken?

**Nur ein Backup.** Die 3-2-1-Regel:

| Regel | Bedeutung |
|-------|-----------|
| **3** Kopien | Original + 2 Backups |
| **2** verschiedene Medien | z.B. lokale Disk + externe Disk |
| **1** Offsite | Eine Kopie an einem anderen Ort |

---

## Schritt für Schritt: Backup einrichten

### Schritt 1: Feature installieren

```powershell
Install-WindowsFeature Windows-Server-Backup -IncludeManagementTools
```

### Schritt 2: Backup-Ziel vorbereiten

```powershell
# Dedizierte Backup-Disk (oder Netzwerkfreigabe)
# Im Training: Netzwerkfreigabe auf einem anderen Server
New-Item -Path "D:\Backups" -ItemType Directory
New-SmbShare -Name "Backups$" -Path "D:\Backups" -FullAccess "TRAINING\DC01$"
```

### Schritt 3: Vollständiges Server-Backup (einmalig)

```powershell
# Backup-Policy erstellen
$policy = New-WBPolicy

# Was sichern? Alles (Bare Metal)
Add-WBBareMetalRecovery -Policy $policy

# System State (AD, Registry, Boot-Dateien)
Add-WBSystemState -Policy $policy

# Wohin? Netzwerkfreigabe
$target = New-WBBackupTarget -NetworkPath "\\SRV-FS01\Backups$\DC01"
Add-WBBackupTarget -Policy $policy -Target $target

# Backup starten
Start-WBBackup -Policy $policy
```

### Schritt 4: Zeitplan einrichten (täglich)

```powershell
# Zeitplan: Täglich um 22:00 Uhr
$policy = New-WBPolicy
Add-WBBareMetalRecovery -Policy $policy
Add-WBSystemState -Policy $policy
$target = New-WBBackupTarget -NetworkPath "\\SRV-FS01\Backups$\DC01"
Add-WBBackupTarget -Policy $policy -Target $target

# Zeitplan setzen
Set-WBSchedule -Policy $policy -Schedule 22:00

# Policy speichern (aktiviert den Zeitplan)
Set-WBPolicy -Policy $policy -Force
```

### Schritt 5: Backup-Status prüfen

```powershell
# Letztes Backup-Ergebnis
Get-WBSummary | Format-List LastSuccessful*, LastBackup*, NextBackup*

# Backup-Verlauf
Get-WBJob -Previous 5 | Format-Table StartTime, EndTime, JobState, HResult
```

---

## Wiederherstellung

### Einzelne Dateien wiederherstellen

```powershell
# Backup-Versionen anzeigen
$backups = Get-WBBackupSet
$backups | Format-Table BackupTime, BackupTarget

# Datei wiederherstellen
$backup = $backups | Select-Object -Last 1
Start-WBFileRecovery -BackupSet $backup `
    -SourcePath "D:\Freigaben\Projekte\wichtig.docx" `
    -TargetPath "C:\temp\restored" -Recursive
```

### System State Recovery (AD-Wiederherstellung)

Im Notfall (AD beschädigt): Im DSRM (Directory Services Restore Mode) booten und System State wiederherstellen.

```powershell
# Nur im DSRM möglich!
wbadmin start systemstaterecovery -version:<VersionID>
```

---

## Challenge

!!! question "Challenge: Backup & Restore testen"
    1. Erstelle ein Backup des `DC01` (mindestens System State)
    2. Erstelle eine Testdatei: `D:\Freigaben\test-backup.txt`
    3. Erstelle ein zweites Backup
    4. Lösche die Testdatei
    5. Stelle sie aus dem Backup wieder her
    6. Prüfe: Ist der Inhalt identisch?

??? success "Hinweis"
    ```powershell
    # Testdatei erstellen
    "Dies ist ein Backup-Test vom $(Get-Date)" | Set-Content "D:\Freigaben\test-backup.txt"

    # Backup starten
    $policy = New-WBPolicy
    $volume = New-WBVolume -VolumePath "D:"
    Add-WBVolume -Policy $policy -Volume $volume
    $target = New-WBBackupTarget -NetworkPath "\\SRV-FS01\Backups$\DC01"
    Add-WBBackupTarget -Policy $policy -Target $target
    Start-WBBackup -Policy $policy

    # Datei löschen
    Remove-Item "D:\Freigaben\test-backup.txt"

    # Wiederherstellen
    $backup = Get-WBBackupSet | Select-Object -Last 1
    Start-WBFileRecovery -BackupSet $backup `
        -SourcePath "D:\Freigaben\test-backup.txt" `
        -TargetPath "C:\temp"
    ```

---

Weiter zu [Modul 43 – Aufräumen](modul-43-aufräumen.md) →
