# Modul 9 – Routing

## Lernziele

Nach diesem Modul kannst du:

- Erklären warum Routing zwischen Subnetzen nötig ist
- Die Rolle "Routing and Remote Access (RRAS)" installieren
- Windows Server als Router zwischen zwei Subnetzen konfigurieren
- NAT (Network Address Translation) einrichten für Internetzugang
- Statische Routen erstellen und Routing-Tabellen lesen
- Mit `tracert` und `Test-NetConnection` den Netzwerkpfad verfolgen

---

## Hintergrund: Routing – der Postbote zwischen Netzwerken

Du hast in Modul 8 zwei Subnetze erstellt: eines für Server (`192.168.100.0/24`) und eines für Clients (`192.168.200.0/24`). Das Problem: Die beiden können noch nicht miteinander sprechen!

Stell dir zwei Stadtteile vor, getrennt durch einen Fluss. Ohne Brücke kommt niemand rüber. Ein **Router** ist diese Brücke – er kennt beide Seiten und leitet Pakete von einem Netz ins andere.

| Ohne Routing | Mit Routing |
|---|---|
| Server-Netz ist isoliert | Client kann Server erreichen |
| Client-Netz ist isoliert | Server kann Client antworten |
| Kein Internet für VMs | NAT ermöglicht Internetzugriff |

### NAT – Raus ins Internet

**NAT (Network Address Translation)** übersetzt private IP-Adressen in eine öffentliche. Wie ein Pförtner, der Briefe von "Raum 203" in "Firma XY, Hauptstraße 1" umschreibt, damit die Antwort den Weg zurück findet.

---

## Schritt für Schritt: RRAS als Router einrichten

### Schritt 1: RRAS-Rolle installieren

Auf dem Server, der als Router dienen soll (z.B. `DC01` oder ein dedizierter `SRV-RTR01`):

```powershell
Install-WindowsFeature Routing -IncludeManagementTools
```

### Schritt 2: RRAS aktivieren

1. Öffne den **Server Manager** → **Tools** → **Routing und RAS**
2. Rechtsklick auf den Servernamen → **Routing und RAS konfigurieren und aktivieren**
3. Wähle **Benutzerdefinierte Konfiguration**
4. Aktiviere **LAN-Routing** und **NAT**
5. Klicke auf **Fertig stellen** → **Dienst starten**

### Schritt 3: NAT konfigurieren

1. Im RRAS-Fenster: Erweitere **IPv4** → Rechtsklick auf **NAT**
2. **Neues Interface** → Wähle das **externe** Netzwerk-Interface (mit Internetzugang)
3. Wähle **Öffentliches Interface mit NAT** → OK

### Schritt 4: Routing testen

Vom Client-Netz aus:

```powershell
# Kann der Client den Router erreichen?
Test-NetConnection -ComputerName 192.168.200.1

# Kann der Client den Server erreichen (anderes Subnetz)?
Test-NetConnection -ComputerName 192.168.100.10

# Route verfolgen
tracert 192.168.100.10
```

!!! tip "PowerShell-Alternative zur RRAS-GUI"
    ```powershell
    # RRAS aktivieren (nur LAN-Routing):
    Install-RemoteAccess -VpnType RoutingOnly
    
    # Routing-Tabelle anzeigen:
    Get-NetRoute | Format-Table DestinationPrefix, NextHop, InterfaceAlias
    ```

---

## Challenge

!!! question "Challenge: Routing verifizieren"
    Stelle sicher, dass ein Client im Subnetz `192.168.200.0/24` den Domain Controller `DC01` (IP `192.168.100.10`) per Ping und per DNS-Namensauflösung erreichen kann. Dokumentiere die Route mit `tracert`.

??? success "Hinweis"
    1. Prüfe ob die Clients als Standard-Gateway `192.168.200.1` haben
    2. Prüfe ob der DNS-Server auf `192.168.100.10` (DC01) zeigt
    3. Teste: `Test-NetConnection 192.168.100.10 -Port 53` (DNS erreichbar?)
    4. Teste: `Resolve-DnsName dc01.training.local` (Namensauflösung klappt?)
    5. `tracert dc01.training.local` zeigt den Hop über den Router

---

Weiter zu [Modul 10 – Windows-Firewall](modul-10-firewall.md) →
