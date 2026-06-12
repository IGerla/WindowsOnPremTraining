# Trainer-Setup: Hyper-V-Host vorbereiten

!!! warning "Diese Seite ist für Trainer/Betreuer"
    Die folgenden Schritte müssen **einmalig** vom Trainer oder IT-Betreuer durchgeführt werden, bevor die Teilnehmer mit dem Training starten können.

---

## Voraussetzungen

### Hardware (Hyper-V-Host)

| Anforderung | Minimum | Empfohlen |
|-------------|---------|-----------|
| CPU | 8 Kerne, VT-x/AMD-V | 16+ Kerne |
| RAM | 32 GB | 64+ GB |
| Speicher | 500 GB SSD | 1 TB NVMe SSD |
| Netzwerk | 1 Gbit Ethernet | 10 Gbit oder 2x 1 Gbit |

**Pro Teilnehmer** rechne mit ca. 8–12 GB RAM und 80–120 GB Speicher (für 2–3 VMs).

### Software

- Windows Server 2022 (oder Windows 11 Pro/Enterprise mit Hyper-V)
- Hyper-V Rolle installiert
- Windows Server 2022 ISO (Evaluierungsversion oder Volumenlizenz)

---

## Schritt 1: Hyper-V Rolle installieren

Falls noch nicht geschehen:

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
```

---

## Schritt 2: Virtuelles Switch erstellen

Erstelle ein **internes Switch** für das Trainingsnetzwerk:

```powershell
New-VMSwitch -Name "internes Switch" -SwitchType Internal
```

Optional: Falls die VMs Internet-Zugang benötigen (für Updates), erstelle ein **externes Switch**:

```powershell
# Zuerst verfügbare Netzwerkadapter anzeigen:
Get-NetAdapter | Where-Object { $_.Status -eq "Up" }

# Externes Switch erstellen (Adapter-Name anpassen!):
New-VMSwitch -Name "externes Switch" -NetAdapterName "Ethernet" -AllowManagementOS $true
```

---

## Schritt 3: ISO-Dateien bereitstellen

Lege die Windows Server 2022 ISO an einem zentralen Ort ab:

```
C:\ISOs\WindowsServer2022.iso
```

!!! tip "ISO herunterladen"
    Die Evaluierungsversion (180 Tage kostenlos) gibt es unter:
    [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/evaluate-windows-server-2022)

---

## Schritt 4: Netzwerk-Bereich festlegen

Für das Training empfehlen wir folgenden IP-Bereich:

| Einstellung | Wert |
|-------------|------|
| Netzwerk | `192.168.100.0/24` |
| Gateway | `192.168.100.1` (Host-Interface) |
| DC01 (DNS/DHCP) | `192.168.100.10` |
| DHCP-Bereich | `192.168.100.100` – `192.168.100.200` |
| Subnetzmaske | `255.255.255.0` |

Konfiguriere das interne Switch-Interface auf dem Host:

```powershell
# IP des Host-Interfaces für das interne Switch setzen
$adapter = Get-NetAdapter | Where-Object { $_.Name -like "*internes Switch*" }
New-NetIPAddress -InterfaceIndex $adapter.ifIndex -IPAddress 192.168.100.1 -PrefixLength 24
```

---

## Schritt 5: Berechtigungen für Teilnehmer

Die Teilnehmer brauchen Rechte, um VMs zu erstellen und zu verwalten. Erstelle eine Gruppe und weise Hyper-V-Berechtigungen zu:

```powershell
# Lokale Gruppe erstellen
New-LocalGroup -Name "Hyper-V Training" -Description "Teilnehmer des On-Prem Trainings"

# Teilnehmer zur Gruppe hinzufügen
Add-LocalGroupMember -Group "Hyper-V Training" -Member "DOMAIN\teilnehmer1"

# Zur Hyper-V-Administratoren-Gruppe hinzufügen
Add-LocalGroupMember -Group "Hyper-V-Administratoren" -Member "DOMAIN\teilnehmer1"
```

!!! info "Remote-Verwaltung"
    Damit Teilnehmer sich per Hyper-V Manager von ihren PCs verbinden können, müssen sie Mitglied der Gruppe **Hyper-V-Administratoren** auf dem Host sein.

---

## Schritt 6: Ordnerstruktur für Teilnehmer

Erstelle pro Teilnehmer einen Ordner für VMs und Festplatten:

```powershell
$teilnehmer = @("Max", "Anna", "Tim", "Lisa")

foreach ($name in $teilnehmer) {
    New-Item -Path "D:\VMs\$name" -ItemType Directory -Force
    New-Item -Path "D:\VHDs\$name" -ItemType Directory -Force
}
```

---

## Schritt 7: Test-VM für Modul 0

Für Modul 0 (Orientierung) ist es hilfreich, wenn bereits eine Test-VM existiert, die die Teilnehmer sich ansehen können:

```powershell
# Kleine Test-VM erstellen (nur zur Demonstration)
New-VM -Name "TEST-VM" -MemoryStartupBytes 2GB -Generation 2 -SwitchName "internes Switch"
```

---

## Checkliste vor Trainingsbeginn

- [ ] Hyper-V Rolle installiert und funktionsfähig
- [ ] Internes Switch `internes Switch` erstellt
- [ ] Windows Server 2022 ISO unter `C:\ISOs\` bereitgelegt
- [ ] IP-Bereich 192.168.100.0/24 konfiguriert
- [ ] Teilnehmer haben Hyper-V-Administratoren-Rechte
- [ ] Ordnerstruktur pro Teilnehmer angelegt
- [ ] Test-VM für Modul 0 vorhanden
- [ ] Teilnehmer können sich per Hyper-V Manager remote verbinden (Test!)
