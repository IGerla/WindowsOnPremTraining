# Modul 14 – Aufräumen

## Lernziele

Nach diesem Modul kannst du:

- Checkpoints aller VMs aus Lernpfad 2 erstellen
- Die Netzwerk-Topologie dokumentieren
- VMs sauber herunterfahren

---

## Was du in Lernpfad 2 gebaut hast

| VM / Komponente | Rolle | Konfiguration |
|-----------------|-------|---------------|
| `DC01` | Router (RRAS) | NAT, LAN-Routing zwischen Server- und Client-Netz |
| `DC01` | NPS (RADIUS) | Netzwerkrichtlinien für WLAN und VPN |
| `SRV-APP01` | Webserver (IIS) | `training.training.local`, Host Header Bindings |
| `SRV-APP01` | VPN-Server (RRAS) | Remote Access VPN (SSTP/L2TP) |
| Virtuelles Switch | `Client-Netz` | 192.168.200.0/24, internes Switch |
| Firewall | Regeln | HTTP, HTTPS, SMB, RDP, VPN-Ports |

## Netzwerk-Dokumentation

Erstelle ein einfaches Netzwerkdiagramm deiner aktuellen Umgebung:

```
Server-Netz: 192.168.100.0/24
├── DC01           (.10) – AD, DNS, DHCP, NPS, Router
├── SRV-APP01      (.30) – IIS, VPN-Server
└── Gateway/Router (.1)  – Host-Interface

Client-Netz: 192.168.200.0/24
├── CLIENT01       (DHCP) – Domänen-Mitglied
└── Gateway        (.1)   – Host-Interface

Routing: DC01 hat Interfaces in beiden Netzen
NAT: Server-Netz → Internet über externes Switch
VPN-Pool: 192.168.100.100–110
```

---

## Checkpoints erstellen

```powershell
# Alle Training-VMs sichern
$vms = @("DC01", "SRV-APP01", "CLIENT01")
foreach ($vm in $vms) {
    Checkpoint-VM -Name $vm -SnapshotName "LP2-fertig" -ComputerName YOURHOST
}
```

---

## VMs stoppen

```powershell
# Reihenfolge beachten: Clients zuerst, DC zuletzt!
Stop-VM -Name "CLIENT01" -ComputerName YOURHOST
Stop-VM -Name "SRV-APP01" -ComputerName YOURHOST
Stop-VM -Name "DC01" -ComputerName YOURHOST
```

!!! tip "Reihenfolge beim nächsten Start"
    1. `DC01` (DNS/AD muss zuerst da sein)
    2. `SRV-APP01`
    3. `CLIENT01`

---

## Zusammenfassung Lernpfad 2

Du hast in diesem Lernpfad:

- [x] Netzwerke segmentiert (Server-Netz + Client-Netz)
- [x] Routing zwischen Subnetzen eingerichtet
- [x] Windows-Firewall mit eigenen Regeln konfiguriert
- [x] Einen IIS-Webserver mit eigener Website aufgesetzt
- [x] NPS als RADIUS-Server für zentrale Authentifizierung eingerichtet
- [x] Remote-Access-VPN mit RRAS konfiguriert

!!! success "Lernpfad 2 abgeschlossen!"
    Dein Netzwerk ist jetzt professionell segmentiert und abgesichert. Du hast einen Webserver, RADIUS-basierte Netzwerkkontrolle und VPN-Zugang – alles Bausteine, die in echten Unternehmensnetzwerken täglich eingesetzt werden.

---

*Weiter geht's mit Lernpfad 3 – Fileserver & Drucken (in Vorbereitung)*
