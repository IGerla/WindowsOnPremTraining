# Modul 32 – Aufräumen

## Lernziele

Nach diesem Modul kannst du:

- Die PKI-Infrastruktur dokumentieren
- Checkpoints aller VMs erstellen
- VMs sauber herunterfahren

---

## Was du in Lernpfad 5 gebaut hast

| Komponente | Konfiguration |
|------------|---------------|
| Enterprise Root CA | `Training-CA` auf `DC01`, RSA 4096, SHA256, 10 Jahre |
| Auto-Enrollment | Computer-Zertifikate automatisch verteilt per GPO |
| Zertifikatvorlage | `Training-Computer`, `Training-WebServer` |
| HTTPS | `training.training.local` + `intranet.training.local` per TLS |
| CRL | Veröffentlicht unter `http://dc01.training.local/CertEnroll/` |

---

## PKI-Dokumentation

Erstelle eine Übersicht deiner CA-Infrastruktur:

```powershell
# CA-Informationen
certutil -ca | Select-String "Name|Config|Type"

# Alle ausgestellten Zertifikate
certutil -view -restrict "Disposition=20" -out "CommonName,NotAfter,CertificateTemplate" |
    Select-Object -First 20

# Veröffentlichte Vorlagen
Get-CATemplate | Format-Table Name

# CRL-Status
certutil -CRL
```

### Dokumentation (zum Ausdrucken)

| Feld | Wert |
|------|------|
| CA-Name | `Training-CA` |
| CA-Server | `DC01.training.local` |
| Schlüssellänge | 4096 Bit RSA |
| Hash-Algorithmus | SHA256 |
| Root-Zertifikat gültig bis | (dein Datum + 10 Jahre) |
| CRL-Verteilungspunkt | `http://dc01.training.local/CertEnroll/Training-CA.crl` |
| Vorlagen | Training-Computer, Training-WebServer |
| Auto-Enrollment | Aktiv per GPO `Zertifikate – Auto-Enrollment` |

---

## Checkpoints erstellen

```powershell
$vms = @("DC01", "SRV-FS01", "SRV-APP01", "CLIENT01")
foreach ($vm in $vms) {
    Checkpoint-VM -Name $vm -SnapshotName "LP5-fertig" -ComputerName YOURHOST
}
```

---

## VMs stoppen

```powershell
Stop-VM -Name "CLIENT01" -ComputerName YOURHOST
Stop-VM -Name "SRV-FS01" -ComputerName YOURHOST
Stop-VM -Name "SRV-APP01" -ComputerName YOURHOST
Stop-VM -Name "DC01" -ComputerName YOURHOST
```

---

## Zusammenfassung Lernpfad 5

Du hast in diesem Lernpfad:

- [x] PKI-Grundlagen verstanden (Schlüsselpaare, Vertrauensketten, Zertifikate)
- [x] Eine Enterprise Root CA installiert und konfiguriert
- [x] Zertifikatvorlagen erstellt und Auto-Enrollment aktiviert
- [x] HTTPS auf deinen IIS-Webseiten eingerichtet (kein Browser-Warnung!)
- [x] Zertifikate verwaltet: erneuern, sperren, CRL veröffentlichen

!!! success "Lernpfad 5 abgeschlossen!"
    Du betreibst jetzt eine eigene PKI. Alle internen Webseiten sind per HTTPS verschlüsselt, Computer bekommen automatisch Zertifikate und du kannst kompromittierte Zertifikate sperren. In der Praxis ist eine funktionsfähige PKI die Grundlage für viele weitere Sicherheitsfeatures (WLAN 802.1X, VPN-Zertifikate, LDAPS, S/MIME).

---

Weiter zu [Modul 33 – Storage Spaces](modul-33-storage.md) →
