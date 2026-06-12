# Modul 45 – Projekt: Migration

## Lernziele

Nach diesem Modul kannst du:

- Einen bestehenden Server auf eine neue Hardware (VM) migrieren
- Daten und Konfigurationen sicher übertragen
- DNS-Einträge und IP-Adressen umziehen
- Ausfallzeit minimieren durch sorgfältige Planung
- Rollback-Pläne erstellen falls die Migration fehlschlägt

---

## Hintergrund: Migration – der häufigste Job im Admin-Alltag

In der Praxis baust du selten etwas komplett Neues auf. Viel häufiger ist: **bestehende Server auf neue Hardware umziehen**. Alte Server werden abgelöst, Betriebssysteme aktualisiert, oder Server konsolidiert.

### Szenario

> "Der Fileserver `SRV-FS01` läuft auf Windows Server 2016 und muss auf einen neuen Server mit Windows Server 2022 umgezogen werden. Die Freigaben, Berechtigungen und Drucker müssen 1:1 übernommen werden. Die Mitarbeiter dürfen maximal 1 Stunde keinen Zugriff haben."

---

## Migrations-Strategien

| Strategie | Vorteile | Nachteile |
|-----------|----------|-----------|
| **In-Place Upgrade** | Kein neuer Server nötig | Risiko bei Problemen |
| **Parallel aufbauen (Swing Migration)** | Alter Server bleibt als Rollback | Braucht zweiten Server |
| **Robocopy + IP-Tausch** | Minimale Ausfallzeit | Komplexe Planung |

---

## Challenge

!!! question "Challenge: Fileserver-Migration durchführen"
    Migriere den Fileserver `SRV-FS01` auf einen neuen Server `SRV-FS03`:
    
    **Anforderungen:**
    
    1. Erstelle einen neuen Server `SRV-FS03` (Windows Server 2022, Domain Join)
    2. Kopiere alle Freigaben inklusive NTFS-Berechtigungen (Robocopy /COPYALL)
    3. Übertrage die Freigabe-Konfiguration (Export/Import)
    4. Tausche die IP-Adressen (SRV-FS03 bekommt die alte IP von SRV-FS01)
    5. Teste: Clients greifen weiterhin auf `\\SRV-FS01\Freigabe` zu (gleicher Name!)
    
    **Dokumentiere deinen Plan:**
    
    - Reihenfolge der Schritte
    - Wann genau ist die Ausfallzeit?
    - Was ist dein Rollback-Plan?

??? success "Hinweis"
    ```powershell
    # 1. Freigaben exportieren (auf SRV-FS01):
    Get-SmbShare | Where-Object { $_.Special -eq $false } |
        Export-Clixml "C:\temp\shares-export.xml"

    # 2. Daten kopieren (SRV-FS01 → SRV-FS03):
    Robocopy "D:\Freigaben" "\\SRV-FS03\D$\Freigaben" /MIR /COPYALL /DCOPY:T /R:3 /W:5

    # 3. Ab hier: Ausfallzeit beginnt!
    # SRV-FS01: IP auf temporäre IP ändern
    # SRV-FS03: IP auf alte SRV-FS01-IP setzen
    # SRV-FS03: Computername auf SRV-FS01 ändern (oder DNS-Alias)

    # 4. Freigaben auf SRV-FS03 erstellen:
    $shares = Import-Clixml "C:\temp\shares-export.xml"
    foreach ($share in $shares) {
        New-SmbShare -Name $share.Name -Path $share.Path
    }

    # 5. Delta-Robocopy (nur Änderungen seit dem ersten Kopieren):
    Robocopy "D:\Freigaben" "\\SRV-FS03\D$\Freigaben" /MIR /COPYALL /DCOPY:T

    # 6. Testen
    Test-Path "\\SRV-FS01\Freigabename"
    ```
    
    **Rollback-Plan:** SRV-FS01 hat noch alle Daten. IPs zurücktauschen, fertig.

---

Weiter zu [Modul 46 – Projekt: Troubleshooting](modul-46-projekt-troubleshooting.md) →
