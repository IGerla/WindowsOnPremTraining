# Modul 35 – Cluster aufbauen

## Lernziele

Nach diesem Modul kannst du:

- Einen Zwei-Knoten-Failover-Cluster erstellen
- iSCSI-Storage als Shared Storage konfigurieren
- Einen File Share Witness für das Quorum einrichten
- Den Cluster-Manager navigieren und den Cluster-Status prüfen
- Ein manuelles Failover durchführen und testen

---

## Hintergrund: Shared Storage mit iSCSI

Beide Cluster-Nodes brauchen Zugriff auf denselben Speicher. In unserer Trainingsumgebung nutzen wir **iSCSI** – ein Protokoll das Festplattenzugriff über das Netzwerk ermöglicht.

```
┌──────────┐     iSCSI     ┌──────────────────┐
│ SRV-FS01 │◄──────────────►│                  │
└──────────┘                │  iSCSI Target    │
                            │  (auf DC01)      │
┌──────────┐     iSCSI     │                  │
│ SRV-FS02 │◄──────────────►│  Shared Disk     │
└──────────┘                └──────────────────┘
```

---

## Schritt für Schritt: Cluster erstellen

### Schritt 1: iSCSI Target einrichten (auf DC01)

```powershell
# iSCSI Target Server Feature installieren
Install-WindowsFeature FS-iSCSITarget-Server -IncludeManagementTools

# Virtuelle Festplatte für den Cluster erstellen
New-IscsiVirtualDisk -Path "C:\iSCSI\ClusterDisk.vhdx" -Size 20GB
New-IscsiVirtualDisk -Path "C:\iSCSI\Quorum.vhdx" -Size 1GB

# iSCSI Target erstellen
New-IscsiServerTarget -TargetName "ClusterTarget" `
    -InitiatorIds @("IQN:iqn.1991-05.com.microsoft:srv-fs01.training.local",
                    "IQN:iqn.1991-05.com.microsoft:srv-fs02.training.local")

# Disks dem Target zuordnen
Add-IscsiVirtualDiskTargetMapping -TargetName "ClusterTarget" -Path "C:\iSCSI\ClusterDisk.vhdx"
Add-IscsiVirtualDiskTargetMapping -TargetName "ClusterTarget" -Path "C:\iSCSI\Quorum.vhdx"
```

### Schritt 2: iSCSI Initiator konfigurieren (auf beiden Nodes)

```powershell
# iSCSI-Dienst starten
Set-Service MSiSCSI -StartupType Automatic
Start-Service MSiSCSI

# Verbindung zum Target herstellen
New-IscsiTargetPortal -TargetPortalAddress "192.168.100.10"
Connect-IscsiTarget -NodeAddress "iqn.1991-05.com.microsoft:clustertarget" -IsPersistent $true

# Neue Disks anzeigen
Get-Disk | Where-Object { $_.BusType -eq "iSCSI" }
```

### Schritt 3: Cluster erstellen

```powershell
New-Cluster -Name "FS-Cluster" -Node "SRV-FS01","SRV-FS02" `
    -StaticAddress 192.168.100.25 -NoStorage
```

### Schritt 4: Cluster-Disks hinzufügen

```powershell
# Disks dem Cluster hinzufügen
Get-Disk | Where-Object { $_.BusType -eq "iSCSI" } | 
    ForEach-Object { Add-ClusterDisk -InputObject $_ }

# Cluster-Disks anzeigen
Get-ClusterResource | Where-Object { $_.ResourceType -eq "Physical Disk" }
```

### Schritt 5: File Share Witness konfigurieren

```powershell
# Freigabe auf DC01 erstellen (für Quorum)
New-Item -Path "C:\Witness" -ItemType Directory
New-SmbShare -Name "Witness$" -Path "C:\Witness" `
    -FullAccess "TRAINING\FS-Cluster$"

# Quorum konfigurieren
Set-ClusterQuorum -FileShareWitness "\\DC01\Witness$"
```

### Schritt 6: Failover testen

```powershell
# Cluster-Status prüfen
Get-ClusterNode | Format-Table Name, State

# Manuelles Failover: Alle Ressourcen auf den anderen Node verschieben
Move-ClusterGroup -Name "Available Storage" -Node "SRV-FS02"

# Prüfen ob der Cluster weiterhin funktioniert
Get-ClusterGroup | Format-Table Name, OwnerNode, State
```

!!! success "Geschafft!"
    Dein Failover Cluster läuft! Zwei Nodes teilen sich Speicher und können füreinander einspringen.

---

## Challenge

!!! question "Challenge: Ausfall simulieren"
    1. Prüfe auf welchem Node die Cluster-Ressourcen aktuell laufen
    2. Fahre diesen Node herunter (simulierter Ausfall)
    3. Beobachte: Übernimmt der andere Node automatisch?
    4. Starte den ausgefallenen Node wieder – was passiert?

??? success "Hinweis"
    ```powershell
    # Vorher prüfen:
    Get-ClusterGroup | Format-Table Name, OwnerNode, State
    
    # Node herunterfahren (auf dem Host):
    Stop-VM -Name "SRV-FS01" -Force -ComputerName YOURHOST
    
    # Auf SRV-FS02 prüfen (nach ~30 Sekunden):
    Get-ClusterNode | Format-Table Name, State
    Get-ClusterGroup | Format-Table Name, OwnerNode, State
    
    # SRV-FS01 wieder starten:
    Start-VM -Name "SRV-FS01" -ComputerName YOURHOST
    # Der Node kommt zurück, aber Ressourcen bleiben auf SRV-FS02 (kein automatisches Failback)
    ```

---

Weiter zu [Modul 36 – Hochverfügbarer Fileserver](modul-36-ha-fileserver.md) →
