# Modul 12 – NPS & RADIUS

## Lernziele

Nach diesem Modul kannst du:

- Erklären was RADIUS ist und warum zentrale Authentifizierung wichtig ist
- Den Network Policy Server (NPS) installieren und konfigurieren
- RADIUS-Clients (z.B. Switches, WLAN-APs) registrieren
- Netzwerkrichtlinien erstellen (wer darf was im Netzwerk?)
- Den Zusammenhang zwischen NPS, Active Directory und Netzwerkgeräten erklären

---

## Hintergrund: RADIUS – zentraler Netzwerk-Türsteher

Stell dir vor, du hast 20 WLAN-Access-Points und 5 Switches mit Port-Authentifizierung. Jeder soll prüfen, ob ein Benutzer ins Netzwerk darf. Ohne RADIUS müsstest du auf **jedem Gerät einzeln** Benutzerkonten pflegen.

**RADIUS (Remote Authentication Dial-In User Service)** löst das Problem: Alle Netzwerkgeräte fragen einen zentralen Server – den **NPS (Network Policy Server)** – und der prüft gegen Active Directory.

```
Benutzer → WLAN-AP → "Darf Max rein?" → NPS → Active Directory
                                            ↓
                              "Ja, Max ist in Gruppe 'WLAN-User'"
                                            ↓
                    WLAN-AP ← "Zugriff gewährt" ← NPS
```

### Typische Einsatzszenarien

| Szenario | Was passiert |
|----------|-------------|
| WLAN-Anmeldung (802.1X) | Laptop sendet Domänen-Credentials → NPS prüft → Zugriff |
| Switch-Port-Security | PC steckt Kabel ein → Switch fragt NPS → Port wird freigeschaltet |
| VPN-Zugang | Benutzer wählt sich ein → VPN-Server fragt NPS → Tunnel wird aufgebaut |

---

## Schritt für Schritt: NPS installieren

### Schritt 1: NPS-Rolle installieren

```powershell
Install-WindowsFeature NPAS -IncludeManagementTools
```

### Schritt 2: NPS-Konsole öffnen

```powershell
nps.msc
```

Oder: **Server Manager** → **Tools** → **Netzwerkrichtlinienserver**

### Schritt 3: NPS im Active Directory registrieren

Damit NPS die Benutzerkonten aus AD lesen kann:

1. In der NPS-Konsole: Rechtsklick auf **NPS (Lokal)** → **Server in Active Directory registrieren**
2. Bestätige mit **OK**

```powershell
# Oder per PowerShell:
netsh ras add registeredserver
```

### Schritt 4: RADIUS-Client hinzufügen

Ein RADIUS-Client ist das Netzwerkgerät, das den NPS fragt (z.B. ein WLAN-AP):

1. Erweitere **RADIUS-Clients und -Server** → Rechtsklick auf **RADIUS-Clients** → **Neu**
2. Fülle aus:

| Feld | Wert |
|------|------|
| Anzeigename | `WLAN-AP-01` |
| Adresse (IP oder DNS) | `192.168.100.50` |
| Gemeinsamer geheimer Schlüssel | Ein starkes Passwort (wird auch auf dem AP konfiguriert) |

### Schritt 5: Netzwerkrichtlinie erstellen

1. Erweitere **Richtlinien** → Rechtsklick auf **Netzwerkrichtlinien** → **Neu**
2. Richtlinienname: `WLAN-Zugriff für IT-Abteilung`
3. Bedingung hinzufügen: **Windows-Gruppen** → Gruppe `IT-Abteilung` auswählen
4. Zugriffsberechtigung: **Zugriff gewähren**
5. Authentifizierungsmethode: **Microsoft: Geschütztes EAP (PEAP)**

!!! info "In dieser Trainingsumgebung"
    Da wir keine physischen WLAN-APs haben, simulieren wir den RADIUS-Test mit dem Tool `NTRadPing` oder per PowerShell. Das Prinzip bleibt identisch.

---

## Challenge

!!! question "Challenge: NPS-Richtlinie für VPN-Zugriff"
    Erstelle eine NPS-Netzwerkrichtlinie, die nur Mitgliedern der AD-Gruppe `VPN-User` den Zugriff erlaubt. Die Richtlinie soll:
    
    - Bedingung: Mitglied der Gruppe `VPN-User`
    - Authentifizierung: MS-CHAPv2
    - Zugriff: Gewährt

??? success "Hinweis"
    1. Erstelle zuerst die Gruppe `VPN-User` im Active Directory
    2. Füge einen Testbenutzer hinzu
    3. Erstelle die Netzwerkrichtlinie im NPS mit der Bedingung "Windows-Gruppen"
    4. Wähle MS-CHAPv2 als Authentifizierungsmethode

---

Weiter zu [Modul 13 – VPN](modul-13-vpn.md) →
