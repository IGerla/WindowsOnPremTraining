# Modul 36 – Hochverfügbarer Fileserver

## Lernziele

Nach diesem Modul kannst du:

- Eine Fileserver-Cluster-Rolle erstellen
- Den Unterschied zwischen "Fileserver für allgemeine Verwendung" und "Scale-Out Fileserver" erklären
- Hochverfügbare Freigaben auf dem Cluster einrichten
- Den Zugriff auf die Cluster-Freigabe vom Client testen
- Ein Failover der Fileserver-Rolle durchführen und nahtlosen Zugriff verifizieren

---

## Hintergrund: Hochverfügbarer Fileserver

Ein normaler Fileserver hat einen Single Point of Failure: Fällt der Server aus, sind alle Freigaben weg. Ein **Cluster-Fileserver** löst das:

| Standard-Fileserver | Cluster-Fileserver |
|---|---|
| 1 Server, 1 IP | 2+ Server, virtuelle Cluster-IP |
| Server aus = Freigaben weg | Failover auf anderen Node |
| Clients: `\\SRV-FS01\Daten` | Clients: `\\FS-Cluster\Daten` (bleibt immer erreichbar) |

---

## Schritt für Schritt: Fileserver-Rolle im Cluster

### Schritt 1: Fileserver-Rolle zum Cluster hinzufügen

```powershell
# Im Failover Cluster Manager:
# Rollen → Rolle konfigurieren → Dateiserver → "Dateiserver für allgemeine Verwendung"

# Per PowerShell:
Add-ClusterFileServerRole -Name "FS-HA" `
    -Storage "Cluster Disk 1" `
    -StaticAddress 192.168.100.26
```

| Einstellung | Wert |
|-------------|------|
| Cluster-Rolle-Name | `FS-HA` |
| IP-Adresse | `192.168.100.26` |
| Speicher | Cluster Disk 1 (das iSCSI-Volume) |

### Schritt 2: DNS-Eintrag prüfen

Der Cluster erstellt automatisch einen DNS-A-Eintrag für `FS-HA`. Prüfe:

```powershell
Resolve-DnsName "FS-HA.training.local"
```

### Schritt 3: Freigabe auf dem Cluster erstellen

```powershell
# Ordner auf dem Cluster-Disk erstellen
New-Item -Path "E:\Cluster-Daten" -ItemType Directory

# Hochverfügbare Freigabe erstellen
New-SmbShare -Name "Cluster-Daten" `
    -Path "E:\Cluster-Daten" `
    -ScopeName "FS-HA" `
    -FullAccess "Domänen-Admins" `
    -ChangeAccess "Domänen-Benutzer"
```

### Schritt 4: Vom Client zugreifen

```powershell
# Auf CLIENT01:
Test-Path "\\FS-HA\Cluster-Daten"
New-PSDrive -Name "X" -PSProvider FileSystem -Root "\\FS-HA\Cluster-Daten" -Persist
```

### Schritt 5: Failover testen

```powershell
# Aktuelle Owner-Node prüfen
Get-ClusterGroup "FS-HA" | Format-Table Name, OwnerNode, State

# Failover erzwingen
Move-ClusterGroup "FS-HA" -Node "SRV-FS02"

# Sofort auf dem Client testen – Freigabe muss weiterhin erreichbar sein!
Test-Path "\\FS-HA\Cluster-Daten"
```

!!! success "Geschafft!"
    Die Freigabe `\\FS-HA\Cluster-Daten` ist hochverfügbar. Selbst wenn ein Node ausfällt, bleibt sie erreichbar – die Clients merken bestenfalls eine kurze Unterbrechung von wenigen Sekunden.

---

## Challenge

!!! question "Challenge: Nahtloses Failover verifizieren"
    1. Erstelle eine Testdatei auf `\\FS-HA\Cluster-Daten\test.txt`
    2. Starte auf dem Client ein Skript das alle 2 Sekunden auf die Freigabe zugreift
    3. Erzwinge in der Zwischenzeit ein Failover
    4. Prüfe: Wie viele Zugriffe sind fehlgeschlagen?

??? success "Hinweis"
    ```powershell
    # Auf CLIENT01 – Zugriffstest starten:
    1..60 | ForEach-Object {
        $result = Test-Path "\\FS-HA\Cluster-Daten\test.txt"
        "[$(Get-Date -Format 'HH:mm:ss')] Erreichbar: $result"
        Start-Sleep -Seconds 2
    }
    
    # Während das läuft – auf einem der Nodes:
    Move-ClusterGroup "FS-HA" -Node "SRV-FS02"
    ```

---

Weiter zu [Modul 37 – Hyper-V Replica](modul-37-replica.md) →
