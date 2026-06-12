# Modul 20 – Aufräumen

## Lernziele

Nach diesem Modul kannst du:

- Checkpoints aller VMs aus Lernpfad 3 erstellen
- Die Freigaben und Berechtigungen dokumentieren
- VMs sauber herunterfahren

---

## Was du in Lernpfad 3 gebaut hast

| VM / Komponente | Rolle | Konfiguration |
|-----------------|-------|---------------|
| `SRV-FS01` | Fileserver | SMB-Freigaben, NTFS-Berechtigungen |
| `SRV-FS01` | DFS-Namespace-Server | `\\training.local\dfs\` |
| `SRV-FS01` | FSRM | Quotas, Datei-Screening |
| `SRV-FS01` | Druckserver | Drucker im AD veröffentlicht, GPO-Verteilung |

## Freigaben-Dokumentation

Erstelle eine Übersicht deiner Freigaben:

```powershell
# Alle Freigaben mit Berechtigungen exportieren
Get-SmbShare | Where-Object { $_.Special -eq $false } | ForEach-Object {
    $share = $_
    $access = Get-SmbShareAccess -Name $share.Name
    [PSCustomObject]@{
        Name = $share.Name
        Pfad = $share.Path
        Beschreibung = $share.Description
        Berechtigungen = ($access | ForEach-Object { "$($_.AccountName): $($_.AccessRight)" }) -join "; "
    }
} | Format-Table -AutoSize
```

| Freigabe | Pfad | Berechtigung |
|----------|------|-------------|
| `Allgemein` | `D:\Freigaben\Allgemein` | Domänen-Benutzer: Ändern |
| `IT` | `D:\Freigaben\Abteilungen\IT` | IT-Abteilung: Ändern |
| `Vertrieb` | `D:\Freigaben\Abteilungen\Vertrieb` | Vertrieb: Ändern |
| `Projekte$` | `D:\Freigaben\Projekte` | Projekt-Team: Ändern |

---

## Checkpoints erstellen

```powershell
$vms = @("DC01", "SRV-FS01", "SRV-APP01", "CLIENT01")
foreach ($vm in $vms) {
    Checkpoint-VM -Name $vm -SnapshotName "LP3-fertig" -ComputerName YOURHOST
}
```

---

## VMs stoppen

```powershell
# Reihenfolge: Clients → Member Server → DC
Stop-VM -Name "CLIENT01" -ComputerName YOURHOST
Stop-VM -Name "SRV-FS01" -ComputerName YOURHOST
Stop-VM -Name "SRV-APP01" -ComputerName YOURHOST
Stop-VM -Name "DC01" -ComputerName YOURHOST
```

---

## Zusammenfassung Lernpfad 3

Du hast in diesem Lernpfad:

- [x] SMB-Dateifreigaben erstellt und Berechtigungen konfiguriert
- [x] NTFS-Berechtigungen mit Vererbung und AGDLP-Prinzip gesetzt
- [x] DFS-Namespaces für einheitliche Pfade eingerichtet
- [x] Quotas und Datei-Screening mit FSRM konfiguriert
- [x] Einen Druckserver mit AD-Veröffentlichung und GPO-Verteilung aufgesetzt

!!! success "Lernpfad 3 abgeschlossen!"
    Dein Fileserver `SRV-FS01` ist jetzt ein vollwertiger Datei- und Druckserver. Benutzer greifen über `\\training.local\dfs\` auf alle Freigaben zu, haben kontrollierte Speicherkontingente und bekommen Drucker automatisch per GPO zugewiesen.

---

Weiter zu [Modul 21 – GPO-Grundlagen](modul-21-gpo.md) →
