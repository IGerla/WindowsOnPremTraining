# Modul 29 – Zertifikate ausstellen

## Lernziele

Nach diesem Modul kannst du:

- Zertifikatvorlagen (Certificate Templates) verstehen und anpassen
- Manuelle Zertifikatsanforderungen erstellen (certreq, MMC)
- Auto-Enrollment konfigurieren (Zertifikate automatisch verteilen)
- Web-Enrollment für manuelle Anforderungen einrichten
- Ausgestellte Zertifikate in der CA-Konsole verwalten

---

## Hintergrund: Wie kommt ein Zertifikat auf den Server?

Es gibt drei Wege, ein Zertifikat zu bekommen:

| Methode | Wann nutzen | Automatisiert? |
|---------|------------|---------------|
| **Auto-Enrollment** | Computer/Benutzer-Zertifikate in Masse | Ja (GPO) |
| **Manuelle Anforderung** (MMC/certreq) | Spezielle Server-Zertifikate | Nein |
| **Web-Enrollment** | Benutzer ohne AD-Zugang, Testanfragen | Nein |

### Zertifikatvorlagen

Vorlagen definieren die "Bauanleitung" für Zertifikate:

- Wofür darf das Zertifikat genutzt werden (Server Auth, Client Auth, Code Signing)?
- Wie lange ist es gültig?
- Welche Schlüssellänge?
- Wer darf es anfordern?

---

## Schritt für Schritt: Auto-Enrollment einrichten

### Schritt 1: Zertifikatvorlage duplizieren und anpassen

1. Öffne `certtmpl.msc` (Zertifikatvorlagen-Konsole)
2. Rechtsklick auf **Computer** → **Vorlage duplizieren**
3. Konfiguriere die neue Vorlage:

| Reiter | Einstellung | Wert |
|--------|-------------|------|
| Allgemein | Vorlagenanzeigename | `Training-Computer` |
| Allgemein | Gültigkeitsdauer | 2 Jahre |
| Sicherheit | Domänen-Computer | Lesen + Registrieren + Automatisch registrieren |
| Antragstellername | Aus AD-Informationen erstellen | ✅ |

4. **OK**

### Schritt 2: Vorlage auf der CA veröffentlichen

```powershell
# In der CA-Konsole (certsrv.msc):
# Rechtsklick auf "Zertifikatvorlagen" → Neu → Auszustellende Zertifikatvorlage
# → "Training-Computer" auswählen

# Oder per PowerShell:
Add-CATemplate -Name "Training-Computer" -Force
```

### Schritt 3: Auto-Enrollment per GPO aktivieren

1. GPO erstellen: `Zertifikate – Auto-Enrollment`
2. Bearbeiten → **Computerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Sicherheitseinstellungen** → **Richtlinien für öffentliche Schlüssel**
3. Doppelklick auf **Zertifikatdienstclient – Automatische Registrierung**
4. **Aktiviert** + beide Häkchen setzen:
   - ✅ Abgelaufene Zertifikate erneuern
   - ✅ Ausstehende Zertifikate aktualisieren und gesperrte entfernen

5. GPO mit der Domäne oder OU `Computer` verknüpfen

### Schritt 4: Testen

```powershell
# Auf einem Client: GPO aktualisieren + Zertifikat anfordern
gpupdate /force
certutil -pulse

# Prüfen ob das Zertifikat angekommen ist:
Get-ChildItem Cert:\LocalMachine\My | 
    Where-Object { $_.Issuer -like "*Training-CA*" } |
    Format-Table Subject, NotAfter, Issuer
```

!!! success "Geschafft!"
    Alle Domänen-Computer bekommen jetzt automatisch ein Zertifikat von deiner CA. Das brauchst du z.B. für WLAN-Authentifizierung (802.1X) oder LDAPS.

---

## Manuelle Anforderung (für Webserver)

Für einen IIS-Webserver brauchst du ein Zertifikat mit einem bestimmten DNS-Namen:

### Per IIS-Manager

1. IIS-Manager öffnen → Servername anklicken → **Serverzertifikate**
2. Rechts: **Zertifikatanforderung erstellen...**
3. Fülle aus:

| Feld | Wert |
|------|------|
| Allgemeiner Name (CN) | `training.training.local` |
| Organisation | `Training GmbH` |
| Schlüssellänge | 2048 |

4. Speichere die Anforderung als `request.txt`
5. Auf dem CA-Server: `certreq -submit request.txt`
6. Zurück im IIS: **Zertifikatanforderung abschließen** → Zertifikat importieren

### Per PowerShell (einfacher)

```powershell
# Zertifikat direkt bei der Enterprise CA anfordern:
$cert = Get-Certificate -Template "WebServer" `
    -DnsName "training.training.local","intranet.training.local" `
    -CertStoreLocation Cert:\LocalMachine\My

$cert.Certificate | Format-List Subject, DnsNameList, NotAfter
```

---

## Challenge

!!! question "Challenge: Webserver-Zertifikat ausstellen"
    1. Erstelle eine Zertifikatvorlage `Training-WebServer` (Duplikat von "Web Server")
    2. Veröffentliche sie auf der CA
    3. Fordere ein Zertifikat für `training.training.local` an
    4. Prüfe ob das Zertifikat im Speicher `Cert:\LocalMachine\My` liegt

??? success "Hinweis"
    ```powershell
    # Vorlage veröffentlichen (nach dem Erstellen in certtmpl.msc):
    Add-CATemplate -Name "Training-WebServer" -Force

    # Zertifikat anfordern:
    Get-Certificate -Template "Training-WebServer" `
        -DnsName "training.training.local" `
        -CertStoreLocation Cert:\LocalMachine\My

    # Prüfen:
    Get-ChildItem Cert:\LocalMachine\My | 
        Where-Object { $_.Subject -like "*training.training.local*" }
    ```

---

Weiter zu [Modul 30 – HTTPS mit eigenem Zertifikat](modul-30-https.md) →
