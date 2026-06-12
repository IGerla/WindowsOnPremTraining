# Modul 31 – Zertifikat-Verwaltung

## Lernziele

Nach diesem Modul kannst du:

- Zertifikate erneuern (Renewal) bevor sie ablaufen
- Zertifikate sperren und Sperrlisten (CRL) verwalten
- Zertifikatvorlagen erstellen und Berechtigungen steuern
- Den Zertifikatspeicher mit PowerShell verwalten
- Probleme mit abgelaufenen oder gesperrten Zertifikaten diagnostizieren

---

## Hintergrund: Zertifikate leben nicht ewig

Ein Zertifikat hat ein Ablaufdatum – danach funktioniert es nicht mehr. Das ist beabsichtigt (Sicherheit), aber du musst dich darum kümmern:

| Situation | Was passiert |
|-----------|-------------|
| Zertifikat abgelaufen | Browser zeigt Warnung, Dienst verweigert Verbindung |
| Zertifikat gesperrt (revoked) | Browser zeigt Warnung, Verbindung wird abgelehnt |
| CRL nicht erreichbar | Manche Clients akzeptieren das Zertifikat trotzdem (soft-fail) |

---

## Zertifikate erneuern (Renewal)

### Automatische Erneuerung

Bei Auto-Enrollment (Modul 29 konfiguriert) werden Zertifikate automatisch erneuert, bevor sie ablaufen. Die GPO-Einstellung "Abgelaufene Zertifikate erneuern" sorgt dafür.

### Manuelle Erneuerung

Für manuell angeforderte Zertifikate (z.B. Webserver):

```powershell
# Zertifikat finden, das bald abläuft
$expiring = Get-ChildItem Cert:\LocalMachine\My | 
    Where-Object { $_.NotAfter -lt (Get-Date).AddDays(30) -and $_.NotAfter -gt (Get-Date) }
$expiring | Format-Table Subject, NotAfter

# Erneuerung anfordern
certreq -enroll -machine -q "Training-WebServer"
```

Im IIS-Manager: **Serverzertifikate** → Zertifikat auswählen → **Erneuern**

---

## Zertifikate sperren (Revocation)

Wann ein Zertifikat gesperrt werden muss:

- Privater Schlüssel wurde kompromittiert (z.B. Server gehackt)
- Server wird außer Betrieb genommen
- Mitarbeiter verlässt das Unternehmen (Benutzer-Zertifikat)

### Zertifikat sperren

```powershell
# In der CA-Konsole (certsrv.msc):
# Ausgestellte Zertifikate → Rechtsklick auf das Zertifikat → Zertifikat sperren

# Per PowerShell:
# Seriennummer des zu sperrenden Zertifikats finden:
certutil -view -restrict "CommonName=training.training.local" -out "SerialNumber"

# Sperren:
certutil -revoke <SERIENNUMMER> 1
# Grund: 1=KeyCompromise, 2=CACompromise, 3=AffiliationChanged, 4=Superseded, 5=CessationOfOperation
```

### Sperrliste (CRL) veröffentlichen

Die CRL ist eine Liste aller gesperrten Zertifikate. Clients laden sie regelmäßig herunter, um zu prüfen ob ein Zertifikat noch gültig ist.

```powershell
# CRL manuell veröffentlichen:
certutil -CRL

# CRL-Verteilungspunkt prüfen:
certutil -getreg CA\CRLPublicationURLs

# CRL anzeigen:
certutil -URL "http://dc01.training.local/CertEnroll/Training-CA.crl"
```

---

## Zertifikatvorlagen verwalten

### Neue Vorlage erstellen

1. `certtmpl.msc` öffnen
2. Bestehende Vorlage rechtsklicken → **Vorlage duplizieren**
3. Anpassen:

| Reiter | Was konfigurieren |
|--------|-------------------|
| **Allgemein** | Name, Gültigkeitsdauer |
| **Antragstellername** | Aus AD oder vom Antragsteller angegeben |
| **Erweiterungen** | Key Usage, Enhanced Key Usage |
| **Sicherheit** | Wer darf anfordern / auto-enrollen |
| **Kryptografie** | Schlüssellänge, Algorithmus |

### Vorlage veröffentlichen/entfernen

```powershell
# Vorlage veröffentlichen (auf der CA verfügbar machen):
Add-CATemplate -Name "MeineVorlage" -Force

# Vorlage entfernen (nicht mehr anbieten):
Remove-CATemplate -Name "MeineVorlage" -Force

# Alle veröffentlichten Vorlagen anzeigen:
Get-CATemplate | Format-Table Name
```

---

## Troubleshooting

### Häufige Probleme

| Problem | Ursache | Lösung |
|---------|---------|--------|
| Browser: "Zertifikat nicht vertrauenswürdig" | Root CA nicht im Trust Store | GPO prüfen, `gpupdate /force` |
| Browser: "Zertifikat abgelaufen" | NotAfter überschritten | Zertifikat erneuern |
| Browser: "Name stimmt nicht überein" | CN/SAN passt nicht zur URL | Neues Zertifikat mit korrektem DNS-Namen |
| Auto-Enrollment funktioniert nicht | Berechtigung auf Vorlage fehlt | Sicherheits-Reiter prüfen |

### Diagnose-Befehle

```powershell
# Alle Zertifikate mit Ablaufdatum anzeigen
Get-ChildItem Cert:\LocalMachine\My | 
    Sort-Object NotAfter |
    Format-Table Subject, NotAfter, Issuer

# Zertifikatskette validieren
certutil -verify -urlfetch "C:\temp\certificate.cer"

# CRL erreichbar?
certutil -URL "http://dc01.training.local/CertEnroll/Training-CA.crl"

# Auto-Enrollment testen
certutil -pulse
```

---

## Challenge

!!! question "Challenge: Zertifikat-Lifecycle"
    Simuliere den kompletten Lebenszyklus eines Zertifikats:

    1. Fordere ein neues Computerzertifikat an (Auto-Enrollment oder manuell)
    2. Prüfe das Zertifikat (Subject, Issuer, Ablaufdatum, Schlüssellänge)
    3. Sperre das Zertifikat auf der CA
    4. Veröffentliche die neue CRL
    5. Prüfe auf dem Client, ob das Zertifikat als "gesperrt" erkannt wird

??? success "Hinweis"
    ```powershell
    # 1. Anfordern
    certutil -pulse
    $cert = Get-ChildItem Cert:\LocalMachine\My | Select-Object -Last 1

    # 2. Prüfen
    $cert | Format-List Subject, Issuer, NotAfter, SerialNumber

    # 3. Sperren (auf der CA)
    certutil -revoke $cert.SerialNumber 4  # 4 = Superseded

    # 4. CRL veröffentlichen
    certutil -CRL

    # 5. Prüfen
    certutil -verify -urlfetch $cert.Thumbprint
    ```

---

Weiter zu [Modul 32 – Aufräumen](modul-32-aufräumen.md) →
