# Modul 11 – Webserver IIS

## Lernziele

Nach diesem Modul kannst du:

- Erklären was IIS (Internet Information Services) ist und wofür man es nutzt
- Die IIS-Rolle installieren und die Verwaltungskonsole nutzen
- Eine eigene Website erstellen und hosten
- Bindings (Port, Hostname, IP) konfigurieren
- Mehrere Websites auf einem Server betreiben (Host Header)
- Die Standardseite über den Browser aus dem Client-Netz aufrufen

---

## Hintergrund: IIS – dein eigener Webserver

Wenn du eine Website im Browser aufrufst, antwortet irgendwo ein **Webserver**. Im Microsoft-Umfeld ist das meistens **IIS (Internet Information Services)** – Microsofts Webserver, der in Windows Server eingebaut ist.

| Apache/nginx (Linux) | IIS (Windows) |
|---|---|
| Konfiguration per Textdatei | Konfiguration per GUI + PowerShell |
| Oft für PHP/Python | Oft für ASP.NET / .NET-Apps |
| Kostenlos, Open Source | Kostenlos, in Windows Server enthalten |

IIS kann mehr als nur Webseiten ausliefern:

- Statische Websites (HTML, CSS, JS)
- Dynamische Webanwendungen (ASP.NET, PHP)
- FTP-Server
- WebDAV (Dateizugriff per Web)

---

## Schritt für Schritt: IIS installieren und Website hosten

### Schritt 1: IIS-Rolle installieren

```powershell
Install-WindowsFeature Web-Server -IncludeManagementTools
```

Nach der Installation erreichst du sofort die IIS-Standardseite: Öffne auf dem Server einen Browser und gehe zu `http://localhost`.

### Schritt 2: IIS-Manager öffnen

```powershell
# IIS-Manager starten:
inetmgr
```

Oder: **Server Manager** → **Tools** → **Internetinformationsdienste (IIS)-Manager**

### Schritt 3: Eigene Website erstellen

1. Im IIS-Manager: Erweitere den Server → Rechtsklick auf **Websites** → **Website hinzufügen**
2. Fülle aus:

| Feld | Wert |
|------|------|
| Websitename | `Training-Web` |
| Physischer Pfad | `C:\inetpub\training` |
| Bindung - Port | `80` |
| Hostname | `training.training.local` |

3. Erstelle vorher den Ordner und eine Testseite:

```powershell
# Ordner erstellen
New-Item -Path "C:\inetpub\training" -ItemType Directory

# Einfache Testseite erstellen
@"
<!DOCTYPE html>
<html>
<head><title>Training Webserver</title></head>
<body>
    <h1>Willkommen auf dem Trainings-Webserver!</h1>
    <p>Server: $env:COMPUTERNAME</p>
    <p>Datum: $(Get-Date)</p>
</body>
</html>
"@ | Set-Content -Path "C:\inetpub\training\index.html"
```

### Schritt 4: DNS-Eintrag erstellen

Damit `training.training.local` funktioniert, braucht es einen DNS-Eintrag:

```powershell
# Auf dem DC (DNS-Server):
Add-DnsServerResourceRecordA -ZoneName "training.local" `
    -Name "training" -IPv4Address "192.168.100.30"
```

### Schritt 5: Vom Client testen

Öffne auf `CLIENT01` einen Browser und navigiere zu:

```
http://training.training.local
```

!!! success "Geschafft!"
    Du siehst deine eigene Webseite – gehostet auf deinem Windows Server!

---

## Challenge

!!! question "Challenge: Zweite Website auf dem gleichen Server"
    Erstelle eine zweite Website `intranet.training.local` auf Port 80 (gleicher Server, gleicher Port – unterschiedlicher Hostname). Die Seite soll eine andere Begrüßung anzeigen. Teste beide Seiten vom Client aus.

??? success "Hinweis"
    Du brauchst:

    1. Neuen Ordner: `C:\inetpub\intranet`
    2. Neue `index.html` mit anderem Inhalt
    3. Neue Website im IIS mit Hostname `intranet.training.local`
    4. Neuen DNS-A-Eintrag `intranet` → gleiche IP
    
    Zwei Websites auf Port 80 funktionieren, weil IIS anhand des **Host-Headers** (Hostname in der URL) entscheidet, welche Website antwortet.

---

Weiter zu [Modul 12 – NPS & RADIUS](modul-12-nps.md) →
