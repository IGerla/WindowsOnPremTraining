# Modul 38 – Aufräumen

## Lernziele

Nach diesem Modul kannst du:

- Die Hochverfügbarkeits-Infrastruktur dokumentieren
- Checkpoints erstellen und VMs stoppen

---

## Was du in Lernpfad 6 gebaut hast

| Komponente | Konfiguration |
|------------|---------------|
| Storage Pool | `DataPool` mit Mirror und Parity Volumes |
| Failover Cluster | `FS-Cluster` (SRV-FS01 + SRV-FS02) |
| Shared Storage | iSCSI Target auf DC01 |
| Quorum | File Share Witness auf DC01 |
| Cluster-Fileserver | `FS-HA` mit hochverfügbarer Freigabe |
| Hyper-V Replica | VM-Replikation auf zweiten Host |

---

## Checkpoints erstellen

```powershell
$vms = @("DC01", "SRV-FS01", "SRV-FS02", "SRV-APP01", "CLIENT01")
foreach ($vm in $vms) {
    Checkpoint-VM -Name $vm -SnapshotName "LP6-fertig" -ComputerName YOURHOST
}
```

---

## Zusammenfassung Lernpfad 6

Du hast in diesem Lernpfad:

- [x] Storage Spaces für software-definiertes RAID konfiguriert
- [x] Failover Clustering Grundlagen verstanden (Quorum, Heartbeat, Nodes)
- [x] Einen Zwei-Knoten-Cluster mit iSCSI-Storage aufgebaut
- [x] Einen hochverfügbaren Fileserver im Cluster betrieben
- [x] Hyper-V Replica für Disaster Recovery eingerichtet

!!! success "Lernpfad 6 abgeschlossen!"
    Deine Infrastruktur ist jetzt ausfallsicher. Der Fileserver übersteht den Ausfall eines Nodes, und per Hyper-V Replica bist du sogar gegen den Totalausfall eines Standorts gewappnet.

---

Weiter zu [Modul 39 – Event Viewer](modul-39-eventviewer.md) →
