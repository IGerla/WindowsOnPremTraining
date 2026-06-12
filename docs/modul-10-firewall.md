# Modul 10 – Windows-Firewall

## Lernziele

Nach diesem Modul kannst du:

- Die Windows Defender Firewall mit erweiterter Sicherheit öffnen und navigieren
- Den Unterschied zwischen eingehenden und ausgehenden Regeln erklären
- Die drei Firewall-Profile (Domäne, Privat, Öffentlich) unterscheiden
- Eigene Firewall-Regeln erstellen (Port-basiert und Programm-basiert)
- Firewall-Regeln per PowerShell verwalten
- Firewall-Einstellungen per GPO zentral verteilen

---

## Hintergrund: Firewall – der Türsteher deines Servers

Stell dir einen Club vor: Der Türsteher entscheidet, wer rein darf und wer nicht. Er hat eine Liste mit Regeln: "VIP-Gäste dürfen immer rein", "Unter 18 draußen bleiben", "Hintertür nur für Personal".

Die **Windows-Firewall** ist der Türsteher für deinen Server. Sie prüft jedes Netzwerkpaket:

- **Eingehend (Inbound)**: Darf diese Verbindung zum Server kommen? (z.B. jemand will auf den Webserver zugreifen)
- **Ausgehend (Outbound)**: Darf der Server diese Verbindung nach außen aufbauen? (z.B. Updates herunterladen)

### Die drei Profile

| Profil | Wann aktiv | Standard-Verhalten |
|--------|-----------|-------------------|
| **Domäne** | Server ist Domänenmitglied und erreicht den DC | Mittlere Sicherheit |
| **Privat** | Manuell gewähltes vertrauenswürdiges Netzwerk | Lockerer |
| **Öffentlich** | Unbekanntes Netzwerk (WLAN im Café) | Sehr strikt |

!!! info "In unserem Training"
    Da alle Server Domänenmitglieder sind, ist immer das **Domänen-Profil** aktiv. Trotzdem solltest du alle Profile korrekt konfigurieren – ein Server könnte mal die Verbindung zum DC verlieren.

---

## Schritt für Schritt: Firewall-Regeln erstellen

### Schritt 1: Firewall-Konsole öffnen

```powershell
# Schnellster Weg:
wf.msc
```

Oder: **Server Manager** → **Tools** → **Windows Defender Firewall mit erweiterter Sicherheit**

### Schritt 2: Eine eingehende Regel erstellen (Port)

Beispiel: HTTP-Verkehr (Port 80) erlauben für den IIS-Webserver:

1. Klicke links auf **Eingehende Regeln**
2. Rechts auf **Neue Regel...**
3. Regeltyp: **Port**
4. Protokoll: **TCP**, Port: `80`
5. Aktion: **Verbindung zulassen**
6. Profile: **Domäne** (die anderen können auch aktiviert bleiben)
7. Name: `HTTP erlauben (Port 80)`

### Schritt 3: Dasselbe per PowerShell

```powershell
# HTTP erlauben
New-NetFirewallRule -DisplayName "HTTP erlauben (Port 80)" `
    -Direction Inbound -Protocol TCP -LocalPort 80 -Action Allow

# HTTPS erlauben
New-NetFirewallRule -DisplayName "HTTPS erlauben (Port 443)" `
    -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow

# Regel anzeigen
Get-NetFirewallRule -DisplayName "HTTP*" | Format-Table DisplayName, Enabled, Direction, Action
```

### Schritt 4: Eine Regel deaktivieren/löschen

```powershell
# Regel deaktivieren (bleibt erhalten, aber inaktiv)
Disable-NetFirewallRule -DisplayName "HTTP erlauben (Port 80)"

# Regel komplett löschen
Remove-NetFirewallRule -DisplayName "HTTP erlauben (Port 80)"
```

!!! warning "Vorsicht mit Outbound-Regeln"
    Standardmäßig erlaubt Windows alle ausgehenden Verbindungen. Wenn du das änderst (Block as default), kann der Server keine Updates mehr laden, keine DNS-Anfragen mehr stellen etc. Ändere das nur, wenn du genau weißt was du tust!

---

## Firewall-Regeln per GPO verteilen

In einer Domäne konfigurierst du Firewall-Regeln nicht auf jedem Server einzeln, sondern zentral per Group Policy:

1. Öffne **Gruppenrichtlinienverwaltung** (auf dem DC)
2. Erstelle eine neue GPO: `Firewall – Webserver`
3. Bearbeiten → **Computerkonfiguration** → **Windows-Einstellungen** → **Sicherheitseinstellungen** → **Windows Defender Firewall mit erweiterter Sicherheit**
4. Erstelle dort die Regeln wie oben – sie werden automatisch auf alle Server in der verknüpften OU angewendet

---

## Challenge

!!! question "Challenge: Firewall für Fileserver konfigurieren"
    Erstelle per PowerShell drei Firewall-Regeln für einen Fileserver:

    1. SMB erlauben (TCP Port 445) – nur aus dem Subnetz `192.168.200.0/24`
    2. Ping erlauben (ICMPv4)
    3. RDP erlauben (TCP Port 3389) – nur aus dem Subnetz `192.168.100.0/24`

    Überprüfe anschließend mit `Get-NetFirewallRule` ob alle drei Regeln aktiv sind.

??? success "Hinweis"
    ```powershell
    # SMB nur aus Client-Netz
    New-NetFirewallRule -DisplayName "SMB aus Client-Netz" `
        -Direction Inbound -Protocol TCP -LocalPort 445 `
        -RemoteAddress 192.168.200.0/24 -Action Allow

    # Ping erlauben
    New-NetFirewallRule -DisplayName "Ping erlauben" `
        -Direction Inbound -Protocol ICMPv4 -Action Allow

    # RDP nur aus Server-Netz
    New-NetFirewallRule -DisplayName "RDP aus Server-Netz" `
        -Direction Inbound -Protocol TCP -LocalPort 3389 `
        -RemoteAddress 192.168.100.0/24 -Action Allow

    # Prüfen:
    Get-NetFirewallRule -DisplayName "SMB*","Ping*","RDP*" |
        Format-Table DisplayName, Enabled, Action
    ```

---

Weiter zu [Modul 11 – Webserver IIS](modul-11-iis.md) →
