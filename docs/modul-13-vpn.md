# Modul 13 – VPN

## Lernziele

Nach diesem Modul kannst du:

- Erklären was ein VPN ist und wofür es genutzt wird
- Den Unterschied zwischen Site-to-Site- und Remote-Access-VPN erklären
- RRAS als VPN-Server konfigurieren (SSTP oder L2TP/IPsec)
- Einen VPN-Benutzer-Zugang einrichten
- Eine VPN-Verbindung von einem Client aus herstellen und testen
- VPN-Zugriff über NPS (RADIUS) absichern

---

## Hintergrund: VPN – sicherer Tunnel durch das Internet

Stell dir vor, du arbeitest von zu Hause und musst auf den Fileserver im Büro zugreifen. Das Problem: Zwischen dir und dem Büro liegt das öffentliche Internet – wie ein offener Marktplatz, auf dem jeder mithören kann.

Ein **VPN (Virtual Private Network)** baut einen verschlüsselten Tunnel durch diesen Marktplatz. Von außen sieht niemand, was im Tunnel passiert – und dein PC verhält sich so, als wäre er direkt im Büronetzwerk.

| Ohne VPN | Mit VPN |
|----------|---------|
| Kein Zugriff auf interne Server | Zugriff wie im Büro |
| Daten unverschlüsselt | Alles verschlüsselt |
| IP des Homeoffice sichtbar | Interne IP zugewiesen |

### VPN-Typen

| Typ | Beschreibung | Typischer Einsatz |
|-----|-------------|-------------------|
| **Remote Access** | Ein einzelner PC verbindet sich ins Firmennetz | Homeoffice, Außendienst |
| **Site-to-Site** | Zwei Standorte werden dauerhaft verbunden | Zweite Niederlassung |

### VPN-Protokolle

| Protokoll | Sicherheit | Hinweis |
|-----------|-----------|---------|
| **SSTP** | Hoch (TLS) | Funktioniert durch Firewalls (Port 443) |
| **L2TP/IPsec** | Hoch | Braucht offene UDP-Ports (500, 4500) |
| **IKEv2** | Hoch | Schneller Reconnect bei Netzwechsel |
| ~~PPTP~~ | ⚠️ Unsicher | Nicht mehr verwenden! |

---

## Schritt für Schritt: Remote-Access-VPN einrichten

### Schritt 1: RRAS für VPN konfigurieren

Falls RRAS bereits aus Modul 9 läuft, erweitere die Konfiguration. Sonst:

```powershell
Install-WindowsFeature DirectAccess-VPN -IncludeManagementTools
```

1. Öffne **Routing und RAS** (rrasmgmt.msc)
2. Rechtsklick auf Server → **Routing und RAS konfigurieren und aktivieren**
3. Wähle **Remotezugriff (DFÜ oder VPN)**
4. Aktiviere **VPN**
5. Wähle das externe Netzwerkinterface (Richtung Internet)
6. IP-Adresszuweisung: **Aus einem Adresspool**
7. Pool: `192.168.100.100` bis `192.168.100.110` (10 VPN-Clients gleichzeitig)
8. RADIUS: **Nein** (oder Ja, wenn du Modul 12 abgeschlossen hast)

### Schritt 2: VPN-Berechtigungen vergeben

Damit ein Benutzer sich per VPN verbinden darf, muss in seinen AD-Eigenschaften oder per NPS-Richtlinie der Zugriff erlaubt sein:

**Option A – Per Benutzer:**

1. Öffne **Active Directory Users & Computers**
2. Doppelklick auf den Benutzer → Reiter **Einwählen**
3. Wähle **Zugriff gestatten**

**Option B – Per NPS-Richtlinie (empfohlen):**

Wie in Modul 12 gelernt: Eine Netzwerkrichtlinie erstellen, die Mitglieder der Gruppe `VPN-User` erlaubt.

### Schritt 3: Firewall-Regeln für VPN

```powershell
# SSTP (Port 443)
New-NetFirewallRule -DisplayName "VPN - SSTP" `
    -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow

# L2TP/IPsec (UDP 500 und 4500)
New-NetFirewallRule -DisplayName "VPN - IKE" `
    -Direction Inbound -Protocol UDP -LocalPort 500 -Action Allow
New-NetFirewallRule -DisplayName "VPN - NAT-T" `
    -Direction Inbound -Protocol UDP -LocalPort 4500 -Action Allow
```

### Schritt 4: VPN-Verbindung auf dem Client erstellen

Auf `CLIENT01`:

1. **Einstellungen** → **Netzwerk & Internet** → **VPN** → **VPN-Verbindung hinzufügen**
2. Fülle aus:

| Feld | Wert |
|------|------|
| VPN-Anbieter | Windows (integriert) |
| Verbindungsname | `Training-VPN` |
| Servername | `192.168.100.30` (IP des VPN-Servers) |
| VPN-Typ | SSTP oder Automatisch |
| Anmeldeinformationen | Benutzername und Kennwort |

3. Verbinde dich mit deinen Domänen-Credentials

!!! success "Geschafft!"
    Du hast eine VPN-Verbindung hergestellt. Der Client bekommt eine IP aus dem VPN-Pool und kann interne Ressourcen erreichen, als wäre er direkt im Netzwerk.

---

## Challenge

!!! question "Challenge: VPN-Zugang mit RADIUS absichern"
    Konfiguriere den VPN-Server so, dass er nicht selbst entscheidet, sondern den **NPS (RADIUS)** aus Modul 12 fragt. Erstelle dann eine NPS-Richtlinie, die nur Mitgliedern der Gruppe `VPN-User` den Zugriff erlaubt. Teste mit einem Benutzer, der in der Gruppe ist (Zugriff klappt) und einem der nicht drin ist (Zugriff verweigert).

??? success "Hinweis"
    1. In RRAS: Rechtsklick auf Server → **Eigenschaften** → **Sicherheit** → Authentifizierungsanbieter auf **RADIUS** ändern → NPS-Server-IP eintragen
    2. Im NPS: RADIUS-Client hinzufügen (der VPN-Server selbst)
    3. NPS-Netzwerkrichtlinie: Bedingung = Gruppe `VPN-User`, Zugriff gewähren
    4. Test-User in die Gruppe aufnehmen → VPN testen ✅
    5. Test-User aus der Gruppe entfernen → VPN wird abgelehnt ❌

---

Weiter zu [Modul 14 – Aufräumen](modul-14-aufräumen.md) →
