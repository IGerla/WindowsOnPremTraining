# Modul 37 – Hyper-V Replica

## Lernziele

Nach diesem Modul kannst du:

- Erklären was Hyper-V Replica ist und wie es sich vom Failover Cluster unterscheidet
- Hyper-V Replica zwischen zwei Hosts konfigurieren
- Eine VM replizieren und den Replikationsstatus überwachen
- Ein geplantes Failover (Planned Failover) durchführen
- Ein ungeplantes Failover (Disaster Recovery) simulieren

---

## Hintergrund: Hyper-V Replica – Disaster Recovery für VMs

Failover Cluster schützt gegen den Ausfall eines Nodes (gleicher Standort). Aber was wenn der **ganze Serverraum** ausfällt (Brand, Wasserschaden, Stromausfall)?

**Hyper-V Replica** repliziert VMs auf einen zweiten Host – idealerweise an einem anderen Standort:

| Failover Cluster | Hyper-V Replica |
|---|---|
| Schutz gegen Node-Ausfall | Schutz gegen Standort-Ausfall |
| Shared Storage nötig | Kein Shared Storage nötig |
| Automatisches Failover | Manuelles Failover (Disaster Recovery) |
| Gleicher Standort | Verschiedene Standorte möglich |
| Sekunden Ausfallzeit | Minuten Ausfallzeit (manuell) |

### Wie funktioniert es?

```
Primärer Host (Standort A)          Replica-Host (Standort B)
┌──────────────┐                    ┌──────────────┐
│    DC01      │ ──Replikation──►  │  DC01 (Copy) │
│  (läuft)     │    alle 5 Min     │  (ausgeschaltet)│
└──────────────┘                    └──────────────┘
```

Die Replica-VM ist eine exakte Kopie, die regelmäßig aktualisiert wird. Sie ist ausgeschaltet und wartet nur darauf, im Notfall gestartet zu werden.

---

## Schritt für Schritt: Hyper-V Replica einrichten

### Schritt 1: Replica auf beiden Hosts aktivieren

Auf **beiden** Hyper-V-Hosts:

1. Hyper-V Manager → Rechtsklick auf Host → **Hyper-V-Einstellungen**
2. **Replikatkonfiguration** → **Diesen Computer als Replikatserver aktivieren**
3. Authentifizierung: **Kerberos (HTTP)** (innerhalb der Domäne)
4. Replikation von allen authentifizierten Servern zulassen
5. Standard-Speicherort für Replikat-Dateien angeben

```powershell
# Per PowerShell:
Set-VMReplicationServer -ReplicationEnabled $true `
    -AllowedAuthenticationType Kerberos `
    -DefaultStorageLocation "D:\Replica"
```

### Schritt 2: Firewall-Regel aktivieren

```powershell
# Auf beiden Hosts:
Enable-NetFirewallRule -DisplayName "Hyper-V Replica HTTP*"
```

### Schritt 3: VM für Replikation konfigurieren

```powershell
# Auf dem primären Host:
Enable-VMReplication -VMName "DC01" `
    -ReplicaServerName "HOST-B.training.local" `
    -ReplicaServerPort 80 `
    -AuthenticationType Kerberos `
    -ReplicationFrequencySec 300  # Alle 5 Minuten
```

### Schritt 4: Initiale Replikation starten

```powershell
Start-VMInitialReplication -VMName "DC01"

# Status prüfen:
Get-VMReplication | Format-Table VMName, State, Health, FrequencySec
```

### Schritt 5: Geplantes Failover

Bei geplantem Failover (z.B. Wartung am primären Host):

```powershell
# 1. Auf dem primären Host: VM stoppen
Stop-VM -Name "DC01"

# 2. Geplantes Failover starten (synchronisiert letzte Änderungen)
Start-VMFailover -VMName "DC01" -Prepare

# 3. Auf dem Replica-Host: Failover abschließen
Start-VMFailover -VMName "DC01"
Start-VM -Name "DC01"
```

---

## Challenge

!!! question "Challenge: Disaster Recovery simulieren"
    Simuliere einen ungeplanten Ausfall:
    
    1. Repliziere eine Test-VM auf den zweiten Host
    2. "Zerstöre" den primären Host (VM hart ausschalten, NICHT herunterfahren)
    3. Starte die Replica-VM auf dem zweiten Host
    4. Prüfe: Wie alt sind die Daten? (Letzter Replikationszeitpunkt)

??? success "Hinweis"
    ```powershell
    # Auf dem Replica-Host:
    # Ungeplantes Failover (primärer Host nicht erreichbar):
    Start-VMFailover -VMName "DC01" -AsTest  # Test-Failover (original bleibt unverändert)
    
    # Oder echtes Failover:
    Start-VMFailover -VMName "DC01"
    Set-VMReplication -VMName "DC01" -Reverse  # Replikationsrichtung umkehren
    Start-VM -Name "DC01"
    
    # Datenverlust prüfen:
    Get-VMReplication -VMName "DC01" | Select-Object LastReplicationTime
    ```
    
    Der maximale Datenverlust entspricht dem Replikationsintervall (z.B. 5 Minuten).

---

Weiter zu [Modul 38 – Aufräumen](modul-38-aufräumen.md) →
