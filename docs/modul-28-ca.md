# Modul 28 – Enterprise CA

## Lernziele

Nach diesem Modul kannst du:

- Den Unterschied zwischen Standalone CA und Enterprise CA erklären
- Die Rolle "Active Directory Certificate Services" (AD CS) installieren
- Eine Enterprise Root CA einrichten und konfigurieren
- Prüfen ob das CA-Zertifikat automatisch an Domänen-Clients verteilt wird
- Die Zertifizierungsstellen-Konsole (certsrv.msc) nutzen

---

## Hintergrund: Warum eine eigene CA?

Für **öffentliche** Webseiten kaufst du Zertifikate bei öffentlichen CAs (Let's Encrypt, DigiCert). Aber für **interne** Systeme brauchst du eine eigene CA:

| Öffentliche CA | Eigene Enterprise CA |
|---|---|
| Kostet Geld pro Zertifikat | Kostenlos (so viele wie du willst) |
| Nur für öffentliche Dienste | Für alles intern (LDAPS, WLAN, VPN, IIS...) |
| Keine Kontrolle über Vorlagen | Eigene Vorlagen erstellen |
| Kein Auto-Enrollment | Zertifikate automatisch verteilen |

### Standalone vs. Enterprise CA

| | Standalone CA | Enterprise CA |
|---|---|---|
| Braucht AD? | Nein | Ja (muss Domänenmitglied sein) |
| Auto-Enrollment | Nein | Ja |
| Zertifikatvorlagen | Nein | Ja |
| Typischer Einsatz | Offline Root CA | Ausstellende CA im Alltag |

Für unser Training richten wir eine **Enterprise Root CA** direkt auf dem DC ein. In der Praxis würde man eine Zwei-Ebenen-Hierarchie nutzen (Offline Root + Online Issuing CA), aber für ein Lab reicht eine Ebene.

---

## Schritt für Schritt: Enterprise CA installieren

### Schritt 1: AD CS Rolle installieren

```powershell
Install-WindowsFeature AD-Certificate -IncludeManagementTools
```

### Schritt 2: CA konfigurieren

1. Im **Server Manager** erscheint oben ein gelbes Warndreieck → Klicke darauf
2. Klicke auf **Active Directory-Zertifikatdienste auf dem Zielserver konfigurieren**
3. Oder per PowerShell den Assistenten starten:

**Konfigurationsassistent:**

| Einstellung | Wert |
|-------------|------|
| Rollendienst | Zertifizierungsstelle |
| Setuptyp | **Unternehmenszertifizierungsstelle** (Enterprise CA) |
| CA-Typ | **Stammzertifizierungsstelle** (Root CA) |
| Privater Schlüssel | Neuen privaten Schlüssel erstellen |
| Kryptografie | RSA, 4096 Bit Schlüssellänge, SHA256 |
| CA-Name | `Training-CA` |
| Gültigkeitszeitraum | 10 Jahre |
| Zertifikatdatenbank | Standard belassen |

```powershell
# Oder komplett per PowerShell:
Install-AdcsCertificationAuthority -CAType EnterpriseRootCA `
    -CACommonName "Training-CA" `
    -KeyLength 4096 `
    -HashAlgorithmName SHA256 `
    -ValidityPeriod Years `
    -ValidityPeriodUnits 10 `
    -Force
```

### Schritt 3: Installation prüfen

```powershell
# CA-Status prüfen
Get-Service certsvc | Format-Table Name, Status, StartType

# CA-Zertifikat anzeigen
Get-ChildItem Cert:\LocalMachine\My | 
    Where-Object { $_.Subject -like "*Training-CA*" } |
    Format-List Subject, Issuer, NotAfter, HasPrivateKey
```

### Schritt 4: CA-Konsole öffnen

```powershell
certsrv.msc
```

Du siehst:
- **Ausgestellte Zertifikate**: Alle bisher ausgestellten Zertifikate
- **Fehlgeschlagene Anforderungen**: Abgelehnte oder fehlerhafte Anfragen
- **Gesperrte Zertifikate**: Zurückgerufene Zertifikate (CRL)
- **Zertifikatvorlagen**: Verfügbare Vorlagen für die Ausstellung

### Schritt 5: Automatische Verteilung prüfen

Da es eine Enterprise CA ist, wird das Root-Zertifikat automatisch per GPO an alle Domänen-Clients verteilt. Prüfe auf `CLIENT01`:

```powershell
# Nach gpupdate auf dem Client:
gpupdate /force

# Root-CA-Zertifikat prüfen:
Get-ChildItem Cert:\LocalMachine\Root | 
    Where-Object { $_.Subject -like "*Training-CA*" }
```

!!! success "Geschafft!"
    Deine Enterprise CA läuft. Alle Domänen-Computer vertrauen ihr automatisch – ohne manuelle Installation des Root-Zertifikats.

---

## Challenge

!!! question "Challenge: CA-Gesundheitscheck"
    Prüfe folgende Punkte deiner frisch installierten CA:

    1. Läuft der Dienst `certsvc`?
    2. Ist das CA-Zertifikat gültig (Ablaufdatum)?
    3. Ist das Root-Zertifikat auf `CLIENT01` im "Vertrauenswürdige Stammzertifizierungsstellen"-Speicher?
    4. Welche Zertifikatvorlagen sind veröffentlicht?

??? success "Hinweis"
    ```powershell
    # 1. Dienst prüfen
    Get-Service certsvc

    # 2. CA-Zertifikat
    certutil -ca.cert | findstr "NotAfter"

    # 3. Auf CLIENT01
    Get-ChildItem Cert:\LocalMachine\Root | Where-Object { $_.Issuer -like "*Training*" }

    # 4. Veröffentlichte Vorlagen
    certutil -CATemplates
    ```

---

Weiter zu [Modul 29 – Zertifikate ausstellen](modul-29-zertifikate.md) →
