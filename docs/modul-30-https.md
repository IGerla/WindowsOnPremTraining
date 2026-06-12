# Modul 30 – HTTPS mit eigenem Zertifikat

## Lernziele

Nach diesem Modul kannst du:

- Ein Zertifikat an eine IIS-Website binden (HTTPS)
- HTTP auf HTTPS umleiten
- Prüfen ob die Verbindung verschlüsselt und vertrauenswürdig ist
- SSL/TLS-Einstellungen verstehen (TLS 1.2 vs. 1.3)
- Zertifikat-Warnungen im Browser interpretieren und beheben

---

## Hintergrund: HTTPS – verschlüsselt und vertrauenswürdig

Ohne HTTPS werden alle Daten im Klartext übertragen – Passwörter, Formulardaten, alles. HTTPS verschlüsselt die Verbindung **und** beweist die Identität des Servers.

| HTTP | HTTPS |
|------|-------|
| Port 80 | Port 443 |
| Klartext | Verschlüsselt (TLS) |
| Keine Identitätsprüfung | Zertifikat beweist: "Ich bin wirklich training.training.local" |
| Browser: ⚠️ "Nicht sicher" | Browser: 🔒 Schloss-Symbol |

### TLS-Versionen

| Version | Status |
|---------|--------|
| SSL 3.0 | ❌ Unsicher, deaktivieren |
| TLS 1.0 | ❌ Veraltet, deaktivieren |
| TLS 1.1 | ❌ Veraltet, deaktivieren |
| TLS 1.2 | ✅ Aktueller Standard |
| TLS 1.3 | ✅ Neuester Standard (schneller) |

---

## Schritt für Schritt: HTTPS auf dem IIS einrichten

### Schritt 1: Zertifikat bereitstellen

Stelle sicher, dass das Webserver-Zertifikat aus Modul 29 im Speicher `Cert:\LocalMachine\My` liegt:

```powershell
Get-ChildItem Cert:\LocalMachine\My | 
    Where-Object { $_.Subject -like "*training*" } |
    Format-Table Subject, Thumbprint, NotAfter
```

### Schritt 2: HTTPS-Binding im IIS erstellen

1. IIS-Manager öffnen → **Sites** → Deine Website auswählen
2. Rechts: **Bindungen...** → **Hinzufügen**

| Feld | Wert |
|------|------|
| Typ | `https` |
| Port | `443` |
| Hostname | `training.training.local` |
| SSL-Zertifikat | Dein Zertifikat aus der Liste wählen |

3. **OK**

```powershell
# Oder per PowerShell:
$cert = Get-ChildItem Cert:\LocalMachine\My | 
    Where-Object { $_.Subject -like "*training.training.local*" }

New-WebBinding -Name "Training-Web" -Protocol "https" -Port 443 `
    -HostHeader "training.training.local"

# Zertifikat an die Bindung binden
$binding = Get-WebBinding -Name "Training-Web" -Protocol "https"
$binding.AddSslCertificate($cert.Thumbprint, "My")
```

### Schritt 3: HTTP → HTTPS Umleitung

Damit Benutzer, die `http://` eingeben, automatisch zu `https://` weitergeleitet werden:

```powershell
# URL Rewrite Modul installieren (falls nicht vorhanden)
# Download: IIS URL Rewrite Module

# Einfache Methode: In der web.config der HTTP-Site
$webConfig = @"
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <httpRedirect enabled="true" destination="https://training.training.local" 
                  httpResponseStatus="Permanent" />
  </system.webServer>
</configuration>
"@
Set-Content -Path "C:\inetpub\training\web.config" -Value $webConfig
```

### Schritt 4: Testen

Auf `CLIENT01` im Browser:

```
https://training.training.local
```

Du solltest:
- 🔒 Das Schloss-Symbol sehen (kein Warnhinweis!)
- Das Zertifikat anzeigen können (ausgestellt von "Training-CA")
- Die Vertrauenskette prüfen können (Training-CA → Server-Zertifikat)

```powershell
# Per PowerShell testen:
Invoke-WebRequest -Uri "https://training.training.local" -UseBasicParsing |
    Select-Object StatusCode, StatusDescription
```

!!! warning "Browser-Warnung?"
    Falls der Browser warnt: Prüfe ob das Root-Zertifikat der Training-CA auf dem Client installiert ist (`Cert:\LocalMachine\Root`). Bei Domänen-Clients sollte das per Auto-Enrollment automatisch passieren.

---

## Challenge

!!! question "Challenge: Zweite HTTPS-Website"
    1. Erstelle ein Zertifikat für `intranet.training.local`
    2. Erstelle eine zweite IIS-Website auf Port 443 mit Hostname `intranet.training.local`
    3. Binde das korrekte Zertifikat an
    4. Teste beide HTTPS-Seiten vom Client: `https://training.training.local` und `https://intranet.training.local`

??? success "Hinweis"
    Zwei HTTPS-Websites auf Port 443 funktionieren dank **SNI (Server Name Indication)** – der Client teilt dem Server beim Verbindungsaufbau mit, welchen Hostnamen er aufruft. IIS wählt dann das passende Zertifikat.

    ```powershell
    # Zertifikat anfordern
    Get-Certificate -Template "Training-WebServer" `
        -DnsName "intranet.training.local" `
        -CertStoreLocation Cert:\LocalMachine\My

    # Website + Binding erstellen
    New-Item "C:\inetpub\intranet" -ItemType Directory
    Set-Content "C:\inetpub\intranet\index.html" "<h1>Intranet</h1>"
    
    New-Website -Name "Intranet" -PhysicalPath "C:\inetpub\intranet" `
        -HostHeader "intranet.training.local"
    ```

---

Weiter zu [Modul 31 – Zertifikat-Verwaltung](modul-31-verwaltung.md) →
