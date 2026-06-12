# Modul 43 – Aufräumen

## Lernziele

Nach diesem Modul kannst du:

- Alle Monitoring-Einstellungen dokumentieren
- Checkpoints erstellen und VMs stoppen

---

## Was du in Lernpfad 7 gebaut hast

| Komponente | Konfiguration |
|------------|---------------|
| Event Viewer | Benutzerdefinierte Ansichten, Event-Forwarding |
| Performance Monitor | Data Collector Sets, Baseline-Daten |
| Windows Admin Center | Zentrale Web-Verwaltung aller Server |
| Windows Server Backup | Täglicher Backup-Plan, System State, getesteter Restore |

---

## Monitoring-Checkliste

| Prüfpunkt | Befehl |
|-----------|--------|
| Fehlgeschlagene Anmeldungen | `Get-WinEvent -FilterHashtable @{LogName='Security';Id=4625} -MaxEvents 5` |
| Unerwartete Neustarts | `Get-WinEvent -FilterHashtable @{LogName='System';Id=6008} -MaxEvents 5` |
| CPU-Auslastung | `Get-Counter '\Processor(_Total)\% Processor Time'` |
| Freier Speicherplatz | `Get-Volume \| Where-Object {$_.SizeRemaining/$_.Size -lt 0.1}` |
| Letztes Backup | `Get-WBSummary \| Select-Object LastSuccessfulBackupTime` |
| Dienste gestoppt | `Get-Service \| Where-Object {$_.StartType -eq 'Automatic' -and $_.Status -ne 'Running'}` |

---

## Checkpoints erstellen

```powershell
$vms = @("DC01", "SRV-FS01", "SRV-FS02", "SRV-APP01", "CLIENT01")
foreach ($vm in $vms) {
    Checkpoint-VM -Name $vm -SnapshotName "LP7-fertig" -ComputerName YOURHOST
}
```

---

## Zusammenfassung Lernpfad 7

Du hast in diesem Lernpfad:

- [x] Event Viewer effektiv genutzt (Filter, Custom Views, wichtige Event-IDs)
- [x] Performance Monitor für Baseline-Messungen und Engpass-Analyse eingesetzt
- [x] Windows Admin Center für zentrale Server-Verwaltung installiert
- [x] Windows Server Backup konfiguriert und einen Restore-Test durchgeführt

!!! success "Lernpfad 7 abgeschlossen!"
    Du kannst jetzt Probleme frühzeitig erkennen, Performance-Engpässe identifizieren und im Notfall Daten wiederherstellen. Das sind unverzichtbare Fähigkeiten für jeden Windows-Admin im täglichen Betrieb.

---

Weiter zu [Modul 44 – Projekt: Neue Niederlassung](modul-44-projekt-niederlassung.md) →
