# Modul 27 – PKI-Grundlagen

## Lernziele

Nach diesem Modul kannst du:

- Erklären was Public Key Infrastructure (PKI) ist und warum sie gebraucht wird
- Den Unterschied zwischen symmetrischer und asymmetrischer Verschlüsselung erklären
- Zertifikate, Schlüsselpaare und Vertrauensketten (Chain of Trust) beschreiben
- Typische Einsatzszenarien für Zertifikate im Unternehmen benennen
- Den Aufbau eines X.509-Zertifikats lesen

---

## Hintergrund: PKI – Ausweise für Computer

Stell dir vor, du betrittst ein Bürogebäude. Der Pförtner fragt: "Wer bist du?" Du zeigst deinen **Personalausweis** – ausgestellt von einer vertrauenswürdigen Stelle (dem Staat). Der Pförtner vertraut dem Ausweis, weil er dem Staat vertraut.

**Zertifikate** sind digitale Ausweise für Computer, Server und Benutzer. Die **Zertifizierungsstelle (CA)** ist der "Staat", der diese Ausweise ausstellt.

| Alltag | PKI-Welt |
|--------|----------|
| Personalausweis | Digitales Zertifikat |
| Staat (Ausstellende Behörde) | Zertifizierungsstelle (CA) |
| Geburtsurkunde → Ausweis | Schlüsselpaar → Zertifikatsanforderung → Zertifikat |
| Ausweis abgelaufen | Zertifikat abgelaufen |
| Ausweis gestohlen → Polizei melden | Zertifikat kompromittiert → Sperrliste (CRL) |

---

## Symmetrische vs. Asymmetrische Verschlüsselung

### Symmetrisch (ein Schlüssel für alles)

```
Absender ──[Schlüssel A]──▶ verschlüsselt ──[Schlüssel A]──▶ Empfänger
```

Problem: Wie tauscht man den Schlüssel sicher aus?

### Asymmetrisch (Schlüsselpaar: öffentlich + privat)

```
Absender ──[öffentlicher Schlüssel]──▶ verschlüsselt ──[privater Schlüssel]──▶ Empfänger
```

| Schlüssel | Wer hat ihn | Was macht er |
|-----------|-------------|--------------|
| **Öffentlich (Public Key)** | Jeder | Verschlüsseln, Signatur prüfen |
| **Privat (Private Key)** | Nur der Besitzer | Entschlüsseln, Signieren |

!!! tip "Merkhilfe"
    - **Öffentlicher Schlüssel** = Briefkasten (jeder kann reinwerfen)
    - **Privater Schlüssel** = Briefkastenschlüssel (nur du kannst rausholen)

---

## Die Vertrauenskette (Chain of Trust)

```
Stammzertifizierungsstelle (Root CA)
    │
    ├── stellt aus ──▶ Untergeordnete CA (Issuing CA)
    │                      │
    │                      ├── stellt aus ──▶ Webserver-Zertifikat
    │                      ├── stellt aus ──▶ Benutzer-Zertifikat
    │                      └── stellt aus ──▶ Computer-Zertifikat
    │
    └── Vertrauen: Wer der Root CA vertraut,
        vertraut automatisch allen Zertifikaten darunter
```

In einer Windows-Domäne installierst du eine **Enterprise CA**. Alle Domänen-Computer vertrauen dieser CA automatisch (das Root-Zertifikat wird per GPO verteilt).

---

## Was steht in einem Zertifikat?

| Feld | Inhalt | Beispiel |
|------|--------|----------|
| Subject (Antragsteller) | Für wen ist das Zertifikat? | `CN=training.training.local` |
| Issuer (Aussteller) | Wer hat es ausgestellt? | `CN=Training-CA` |
| Valid From / To | Gültigkeitszeitraum | 01.01.2026 – 01.01.2028 |
| Public Key | Öffentlicher Schlüssel | RSA 2048 Bit |
| Serial Number | Eindeutige Nummer | `4A:3B:2C:...` |
| Enhanced Key Usage | Wofür darf es genutzt werden? | Server Authentication, Client Authentication |
| CRL Distribution Point | Wo ist die Sperrliste? | `http://ca.training.local/crl` |

Du kannst jedes Zertifikat im Browser oder per PowerShell anschauen:

```powershell
# Alle Zertifikate im lokalen Computer-Store anzeigen
Get-ChildItem Cert:\LocalMachine\My | Format-Table Subject, Issuer, NotAfter

# Ein bestimmtes Zertifikat im Detail:
Get-ChildItem Cert:\LocalMachine\My | Select-Object -First 1 | Format-List *
```

---

## Einsatzszenarien für Zertifikate

| Szenario | Zertifikat-Typ | Warum |
|----------|---------------|-------|
| HTTPS (interne Webseiten) | Webserver-Zertifikat | Verschlüsselte Verbindung, keine Browser-Warnung |
| WLAN-Authentifizierung (802.1X) | Computer-Zertifikat | PC beweist seine Identität im Netzwerk |
| VPN-Verbindung | Server + Client-Zertifikat | Gegenseitige Authentifizierung |
| E-Mail-Verschlüsselung (S/MIME) | Benutzer-Zertifikat | Nur der Empfänger kann lesen |
| Code Signing | Code-Signing-Zertifikat | Beweist, dass Software nicht manipuliert wurde |
| LDAPS | DC-Zertifikat | Verschlüsselte AD-Abfragen |

---

## Challenge

!!! question "Challenge: Zertifikate untersuchen"
    1. Öffne im Browser eine HTTPS-Website (z.B. `https://www.microsoft.com`)
    2. Klicke auf das Schloss-Symbol → Zertifikat anzeigen
    3. Notiere: Subject, Issuer, Gültigkeitszeitraum, Schlüssellänge
    4. Verfolge die Vertrauenskette: Welche Root CA steht ganz oben?
    5. Per PowerShell: Welche Zertifikate sind im Speicher `Cert:\LocalMachine\Root` (vertrauenswürdige Stammzertifizierungsstellen)?

??? success "Hinweis"
    ```powershell
    # Vertrauenswürdige Root CAs anzeigen:
    Get-ChildItem Cert:\LocalMachine\Root | 
        Format-Table Subject, NotAfter, Thumbprint

    # Zählen:
    (Get-ChildItem Cert:\LocalMachine\Root).Count
    ```
    
    Du wirst sehen: Windows vertraut bereits vielen Root CAs (Microsoft, DigiCert, Let's Encrypt, etc.). Deine eigene Enterprise CA wird nach der Installation ebenfalls dort erscheinen.

---

Weiter zu [Modul 28 – Enterprise CA](modul-28-ca.md) →
