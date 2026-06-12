# Modul 33 – Storage Spaces

## Lernziele

Nach diesem Modul kannst du:

- Erklären was Storage Spaces und Storage Pools sind
- Den Unterschied zwischen Simple, Mirror und Parity erklären
- Einen Storage Pool aus mehreren virtuellen Festplatten erstellen
- Virtuelle Datenträger (Virtual Disks) mit Resiliency anlegen
- Storage Spaces als Basis für hochverfügbaren Speicher verstehen

---

## Hintergrund: Storage Spaces – RAID ohne RAID-Controller

Früher brauchtest du teure RAID-Controller, um Festplatten zusammenzufassen und gegen Ausfall abzusichern. **Storage Spaces** macht dasselbe in Software – direkt in Windows Server eingebaut.

| RAID (Hardware) | Storage Spaces (Software) |
|---|---|
| Teurer Controller nötig | In Windows eingebaut (kostenlos) |
| Herstellerabhängig | Funktioniert mit jeder Festplatte |
| Controller kaputt = Daten gefährdet | Flexibel, hot-add von Festplatten |

### Die drei Resiliency-Typen

| Typ | Schutz | Mindest-Disks | Alltags-Vergleich |
|-----|--------|---------------|-------------------|
| **Simple** | Keiner (Striping) | 1 | Einzelnes Notizbuch – weg ist weg |
| **Mirror** | 1-2 Disk-Ausfälle | 2 (Two-Way) / 5 (Three-Way) | Fotokopie – Original + Kopie |
| **Parity** | 1 Disk-Ausfall | 3 | Sudoku-Prinzip – fehlende Zahl berechenbar |

---

## Schritt für Schritt: Storage Pool erstellen

### Vorbereitung: Virtuelle Festplatten hinzufügen

In unserer Trainingsumgebung simulieren wir mehrere Festplatten mit zusätzlichen VHDXs:

```powershell
# Auf dem Hyper-V-Host: 4 zusätzliche VHDXs an die VM anhängen
$vmName = "SRV-FS01"
1..4 | ForEach-Object {
    $path = "D:\VHDs\$vmName-Data$_.vhdx"
    New-VHD -Path $path -SizeBytes 20GB -Dynamic
    Add-VMHardDiskDrive -VMName $vmName -Path $path
}
```

### Schritt 1: Verfügbare Disks anzeigen

```powershell
# Auf SRV-FS01: Neue Disks erkennen
Get-PhysicalDisk | Where-Object { $_.CanPool -eq $true } |
    Format-Table FriendlyName, Size, MediaType, CanPool
```

### Schritt 2: Storage Pool erstellen

```powershell
$disks = Get-PhysicalDisk | Where-Object { $_.CanPool -eq $true }

New-StoragePool -FriendlyName "DataPool" `
    -StorageSubSystemFriendlyName "Windows Storage*" `
    -PhysicalDisks $disks
```

### Schritt 3: Virtual Disk mit Mirror erstellen

```powershell
New-VirtualDisk -StoragePoolFriendlyName "DataPool" `
    -FriendlyName "MirrorDisk" `
    -Size 30GB `
    -ResiliencySettingName Mirror `
    -NumberOfDataCopies 2
```

### Schritt 4: Volume erstellen und formatieren

```powershell
# Disk initialisieren
$vdisk = Get-VirtualDisk -FriendlyName "MirrorDisk" | Get-Disk
Initialize-Disk -Number $vdisk.Number -PartitionStyle GPT

# Partition erstellen und formatieren
New-Partition -DiskNumber $vdisk.Number -UseMaximumSize -DriveLetter E |
    Format-Volume -FileSystem ReFS -NewFileSystemLabel "Daten"
```

!!! success "Geschafft!"
    Du hast ein gespiegeltes Volume `E:\` – selbst wenn eine der physischen Festplatten ausfällt, bleiben deine Daten erhalten.

---

## Challenge

!!! question "Challenge: Parity-Volume erstellen"
    Erstelle einen zweiten Virtual Disk mit Parity-Resiliency (mindestens 3 Disks im Pool nötig). Formatiere ihn als Laufwerk `F:\` mit dem Label "Archiv". Vergleiche die nutzbare Größe von Mirror vs. Parity bei gleicher Rohkapazität.

??? success "Hinweis"
    ```powershell
    New-VirtualDisk -StoragePoolFriendlyName "DataPool" `
        -FriendlyName "ParityDisk" -Size 20GB `
        -ResiliencySettingName Parity

    $pdisk = Get-VirtualDisk -FriendlyName "ParityDisk" | Get-Disk
    Initialize-Disk -Number $pdisk.Number -PartitionStyle GPT
    New-Partition -DiskNumber $pdisk.Number -UseMaximumSize -DriveLetter F |
        Format-Volume -FileSystem ReFS -NewFileSystemLabel "Archiv"
    ```
    
    Mirror braucht 2× den Speicher (50% Effizienz), Parity nur ~33% Overhead (67% Effizienz).

---

Weiter zu [Modul 34 – Failover Cluster Grundlagen](modul-34-cluster.md) →
