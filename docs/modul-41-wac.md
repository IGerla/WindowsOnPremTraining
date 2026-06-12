# Modul 41 – Windows Admin Center

## Lernziele

Nach diesem Modul kannst du:

- Windows Admin Center (WAC) installieren und konfigurieren
- Server über die Web-Oberfläche verwalten (ohne RDP)
- Mehrere Server zentral in einer Oberfläche überwachen
- Rollen und Features über WAC installieren
- PowerShell-Sitzungen direkt im Browser starten

---

## Hintergrund: Windows Admin Center – die moderne Verwaltung

Bisher hast du Server per RDP, MMC-Konsolen und PowerShell verwaltet. **Windows Admin Center (WAC)** vereint alles in einer modernen Web-Oberfläche:

| Klassisch (MMC/RDP) | Windows Admin Center |
|---|---|
| Für jeden Server einzeln verbinden | Alle Server in einer Oberfläche |
| Verschiedene MMC-Konsolen öffnen | Alles im Browser |
| RDP = voller Desktop nötig | Nur was du brauchst |
| Nur lokal oder per VPN | Über HTTPS erreichbar |

---

## Schritt für Schritt: WAC installieren

### Schritt 1: Download und Installation

WAC wird als MSI-Paket installiert – am besten auf einem dedizierten Management-Server oder dem DC:

```powershell
# Download (von Microsoft)
# URL: https://aka.ms/wacdownload

# Installation (Beispiel):
msiexec /i WindowsAdminCenter.msi /qn /L*v wac-install.log SME_PORT=443 SSL_CERTIFICATE_OPTION=generate
```

| Einstellung | Wert |
|-------------|------|
| Port | 443 (HTTPS) |
| Zertifikat | Self-signed (oder eigenes von der CA) |
| Installation auf | `DC01` oder eigenem Management-Server |

### Schritt 2: WAC im Browser öffnen

```
https://dc01.training.local
```

Melde dich mit Domänen-Admin-Credentials an.

### Schritt 3: Server hinzufügen

1. Klicke auf **Hinzufügen** → **Server**
2. Gib den Servernamen ein: `SRV-FS01`, `SRV-APP01`, `CLIENT01`
3. WAC verbindet sich per WinRM (muss auf den Zielservern aktiv sein)

```powershell
# WinRM auf allen Servern aktivieren (falls nicht schon geschehen):
Enable-PSRemoting -Force
```

### Schritt 4: Server verwalten

Klicke auf einen Server – du siehst:

- **Übersicht**: CPU, RAM, Netzwerk in Echtzeit
- **Dateien**: Dateisystem durchsuchen
- **Firewall**: Regeln verwalten
- **Rollen & Features**: Installieren/Deinstallieren
- **PowerShell**: Terminal direkt im Browser
- **Ereignisse**: Event Viewer
- **Updates**: Windows Updates prüfen und installieren

---

## Challenge

!!! question "Challenge: Alle Server zentral verwalten"
    1. Installiere WAC auf `DC01`
    2. Füge alle Training-Server hinzu (SRV-FS01, SRV-APP01, CLIENT01)
    3. Prüfe die CPU-Auslastung aller Server auf einen Blick
    4. Installiere ein Windows Feature über WAC (z.B. Telnet-Client auf CLIENT01)
    5. Öffne eine PowerShell-Sitzung zu `SRV-FS01` direkt im Browser

??? success "Hinweis"
    Falls die Verbindung zu einem Server fehlschlägt:
    
    - WinRM aktiv? `Test-WSMan -ComputerName SRV-FS01`
    - Firewall? WinRM braucht Port 5985 (HTTP) oder 5986 (HTTPS)
    - Berechtigung? Der angemeldete Benutzer muss Admin auf dem Zielserver sein

---

Weiter zu [Modul 42 – Windows Server Backup](modul-42-backup.md) →
