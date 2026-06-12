# Modul 26 – Aufräumen

## Lernziele

Nach diesem Modul kannst du:

- Alle GPOs dokumentieren und exportieren
- Resultant Set of Policy (RSoP) für einen Benutzer/Computer prüfen
- Checkpoints erstellen und VMs stoppen

---

## Was du in Lernpfad 4 gebaut hast

| GPO | Typ | Verknüpft mit |
|-----|-----|---------------|
| `Desktop – Firmenhintergrund` | Benutzer | OU Benutzer |
| `Laufwerke – Vertrieb` | Benutzer | OU Benutzer (Item-Level Targeting) |
| `Arbeitsplatz – Standardbenutzer` | Benutzer | OU Benutzer |
| `Software – Standardprogramme` | Computer | OU Computer |
| `Sicherheit – Passwort-Policy` | Computer | Domäne |
| `Sicherheit – Clients` | Computer | OU Computer |
| `Browser – Unternehmensrichtlinie` | Computer | OU Computer |

---

## GPO-Dokumentation

### Alle GPOs als HTML-Report exportieren

```powershell
# Alle GPOs als Reports speichern
$reportPath = "C:\temp\GPO-Reports"
New-Item -Path $reportPath -ItemType Directory -Force

Get-GPO -All | ForEach-Object {
    $name = $_.DisplayName -replace '[\\/:*?"<>|]', '_'
    Get-GPOReport -Guid $_.Id -ReportType HTML -Path "$reportPath\$name.html"
}

Write-Host "Reports gespeichert in: $reportPath"
```

### GPO-Backup erstellen

```powershell
# Alle GPOs sichern (kann bei Bedarf wiederhergestellt werden)
Backup-GPO -All -Path "C:\temp\GPO-Backup"
```

---

## Resultant Set of Policy (RSoP)

RSoP zeigt dir, welche GPO-Einstellungen **tatsächlich** auf einem bestimmten Computer/Benutzer wirken:

```powershell
# Auf dem Ziel-Client:
gpresult /r                          # Übersicht: Welche GPOs wirken?
gpresult /h C:\temp\rsop.html        # Detaillierter HTML-Report

# Remote für einen bestimmten Computer:
gpresult /s CLIENT01 /user TRAINING\max /h C:\temp\rsop-max.html
```

### GPO-Modellierung (Was-wäre-wenn)

In der Gruppenrichtlinienverwaltung:

1. Rechtsklick auf **Gruppenrichtlinienmodellierung** → **Gruppenrichtlinienmodellierungs-Assistent**
2. Wähle Computer und Benutzer aus
3. Der Assistent zeigt, welche Einstellungen wirken **würden**

---

## Checkpoints erstellen

```powershell
$vms = @("DC01", "SRV-FS01", "SRV-APP01", "CLIENT01")
foreach ($vm in $vms) {
    Checkpoint-VM -Name $vm -SnapshotName "LP4-fertig" -ComputerName YOURHOST
}
```

---

## VMs stoppen

```powershell
Stop-VM -Name "CLIENT01" -ComputerName YOURHOST
Stop-VM -Name "SRV-FS01" -ComputerName YOURHOST
Stop-VM -Name "SRV-APP01" -ComputerName YOURHOST
Stop-VM -Name "DC01" -ComputerName YOURHOST
```

---

## Zusammenfassung Lernpfad 4

Du hast in diesem Lernpfad:

- [x] GPO-Grundlagen verstanden (LSDOU, Vererbung, Erzwingen)
- [x] Netzlaufwerke, Drucker und Ordnerumleitungen per GPO konfiguriert
- [x] Software automatisch per GPO verteilt (MSI-Pakete)
- [x] Sicherheitsrichtlinien eingerichtet (Passwort, Lockout, Audit, AppLocker)
- [x] Den Central Store mit ADMX-Vorlagen eingerichtet

!!! success "Lernpfad 4 abgeschlossen!"
    Group Policy ist eines der mächtigsten Werkzeuge in einer Windows-Domäne. Du kannst jetzt den Arbeitsplatz, die Sicherheit und die Software zentral verwalten – bei 5 oder 5.000 Computern.

---

Weiter zu [Modul 27 – PKI-Grundlagen](modul-27-pki.md) →
