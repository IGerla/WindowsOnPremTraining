# Modul 8 – Netzwerk-Design

## Lernziele

Nach diesem Modul kannst du:

- Den Unterschied zwischen flachem Netzwerk und segmentiertem Netzwerk erklären
- Subnetze planen und Subnetzmasken berechnen
- Virtuelle Switches in Hyper-V erstellen und konfigurieren (intern, extern, privat)
- VLANs auf virtuellen Switches konfigurieren
- Einen Netzwerkplan für die Trainingsumgebung zeichnen

---

## Hintergrund: Warum Netzwerke segmentieren?

Stell dir ein großes Bürogebäude vor: Wenn alle 500 Mitarbeiter in einem einzigen offenen Raum sitzen, wird es laut, unübersichtlich und unsicher. Deshalb gibt es Abteilungen mit eigenen Räumen und Türen.

**Netzwerksegmentierung** ist dasselbe Prinzip für Computer-Netzwerke:

| Flaches Netzwerk (ein Raum) | Segmentiertes Netzwerk (Abteilungen) |
|---|---|
| Alle Geräte sehen sich gegenseitig | Nur Geräte im selben Segment sehen sich |
| Ein Virus breitet sich überall aus | Viren bleiben im Segment eingesperrt |
| Broadcast-Stürme treffen alle | Broadcasts bleiben lokal |
| Keine Kontrolle wer wohin darf | Firewall-Regeln zwischen Segmenten |

### Subnetze (Subnets)

Ein Subnetz ist ein logischer Bereich von IP-Adressen. Die Subnetzmaske bestimmt, welche Adressen zum selben Netz gehören:

| Netzwerk | Subnetzmaske | Adressen | Nutzbar |
|----------|--------------|----------|---------|
| `192.168.100.0/24` | `255.255.255.0` | 256 | 254 Hosts |
| `192.168.100.0/25` | `255.255.255.128` | 128 | 126 Hosts |
| `192.168.100.0/26` | `255.255.255.192` | 64 | 62 Hosts |

### VLANs (Virtual LANs)

VLANs sind virtuelle Netzwerke auf demselben physischen Switch. Ohne VLANs brauchst du für jedes Netzwerk einen eigenen Switch – mit VLANs teilst du einen Switch in mehrere logische Bereiche auf.

### Netzwerkplan für unser Training

```
┌─────────────────────────────────────────────────────┐
│              Hyper-V Virtuelles Netzwerk             │
├─────────────────────────────────────────────────────┤
│                                                     │
│  Subnetz: Server (192.168.100.0/24)                │
│  ┌──────┐  ┌──────────┐  ┌──────────┐            │
│  │ DC01 │  │ SRV-FS01 │  │ SRV-APP01│            │
│  │ .10  │  │ .20      │  │ .30      │            │
│  └──┬───┘  └────┬─────┘  └────┬─────┘            │
│     └────────────┼─────────────┘                   │
│                  │                                  │
│            ┌─────┴─────┐                           │
│            │  Router   │                           │
│            │  .1       │                           │
│            └─────┬─────┘                           │
│                  │                                  │
│  Subnetz: Clients (192.168.200.0/24)              │
│  ┌──────────┐  ┌──────────┐                      │
│  │ CLIENT01 │  │ CLIENT02 │                      │
│  │ DHCP     │  │ DHCP     │                      │
│  └──────────┘  └──────────┘                      │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## Schritt für Schritt: Virtuelle Switches verstehen

### Die drei Switch-Typen in Hyper-V

| Typ | Verbindung | Verwendung |
|-----|-----------|-----------|
| **Extern** | VMs + Host + physisches Netzwerk | Internetzugriff für VMs |
| **Intern** | VMs + Host (kein physisches Netzwerk) | Kommunikation VM ↔ Host |
| **Privat** | Nur VMs untereinander | Isolierte Testnetze |

### Schritt 1: Bestehende Switches anzeigen

```powershell
Get-VMSwitch | Format-Table Name, SwitchType, NetAdapterInterfaceDescription
```

### Schritt 2: Neuen internen Switch für das Client-Netzwerk erstellen

```powershell
New-VMSwitch -Name "Client-Netz" -SwitchType Internal
```

### Schritt 3: IP-Adresse auf dem neuen Switch setzen (Host-Interface)

```powershell
$adapter = Get-NetAdapter | Where-Object { $_.Name -like "*Client-Netz*" }
New-NetIPAddress -InterfaceIndex $adapter.ifIndex -IPAddress 192.168.200.1 -PrefixLength 24
```

### Schritt 4: VLAN-ID auf einem VM-Netzwerkadapter setzen

```powershell
Set-VMNetworkAdapterVlan -VMName "CLIENT01" -Access -VlanId 200
```

!!! tip "Tipp: VLAN-Tagging"
    In Hyper-V kannst du VLANs pro VM-Netzwerkadapter konfigurieren. Das ist praktisch um auf einem einzigen physischen Switch mehrere logische Netze zu betreiben.

---

## Challenge

!!! question "Challenge: Zweites Subnetz erstellen"
    Erstelle einen neuen internen Switch namens `DMZ-Netz` mit dem Adressbereich `10.0.0.0/24`. Setze die IP `10.0.0.1` auf das Host-Interface. Überprüfe mit `Get-VMSwitch` und `Get-NetIPAddress`, dass alles korrekt konfiguriert ist.

??? success "Hinweis"
    ```powershell
    New-VMSwitch -Name "DMZ-Netz" -SwitchType Internal
    $dmz = Get-NetAdapter | Where-Object { $_.Name -like "*DMZ-Netz*" }
    New-NetIPAddress -InterfaceIndex $dmz.ifIndex -IPAddress 10.0.0.1 -PrefixLength 24

    # Überprüfen:
    Get-VMSwitch | Format-Table Name, SwitchType
    Get-NetIPAddress -InterfaceAlias "*DMZ*" | Select-Object IPAddress, PrefixLength
    ```

---

Weiter zu [Modul 9 – Routing](modul-9-routing.md) →
