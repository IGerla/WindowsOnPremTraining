# Modul 39 – Event Viewer

## Lernziele

Nach diesem Modul kannst du:

- Die Windows-Ereignisanzeige navigieren und die wichtigsten Protokolle benennen
- Ereignisse filtern (nach ID, Quelle, Zeitraum, Schweregrad)
- Kritische Ereignis-IDs kennen (Anmeldung, Absturz, Dienst-Fehler)
- Benutzerdefinierte Ansichten erstellen
- Ereignisse per PowerShell abfragen und exportieren
- Ereignisweiterleitung (Event Forwarding) für zentrale Protokollierung einrichten

---

## Hintergrund: Event Viewer – das Logbuch deines Servers

Stell dir vor, dein Server hat nachts Probleme. Am Morgen läuft alles wieder – aber **was ist passiert?** Der Event Viewer ist das Logbuch: Hier steht jede Anmeldung, jeder Fehler, jeder Neustart.

### Die wichtigsten Protokolle

| Protokoll | Was steht drin |
|-----------|---------------|
| **System** | Hardware, Treiber, Dienste, Neustarts |
| **Anwendung** | Programmfehler, Installationen |
| **Sicherheit** | Anmeldungen, Zugriffe, Audit-Events |
| **Setup** | Windows-Updates, Rollen-Installation |

### Schweregrade

| Symbol | Stufe | Bedeutung |
|--------|-------|-----------|
| ❌ | Kritisch | System/Dienst abgestürzt |
| ⚠️ | Fehler | Etwas ist fehlgeschlagen |
| ⚡ | Warnung | Potentielles Problem |
| ℹ️ | Information | Normaler Vorgang (zur Info) |

---

## Wichtige Event-IDs

| Event-ID | Protokoll | Bedeutung |
|----------|-----------|-----------|
| 4624 | Sicherheit | Erfolgreiche Anmeldung |
| 4625 | Sicherheit | Fehlgeschlagene Anmeldung |
| 4740 | Sicherheit | Benutzerkonto gesperrt |
| 6005 | System | Eventlog-Dienst gestartet (= Server gestartet) |
| 6006 | System | Eventlog-Dienst gestoppt (= Server heruntergefahren) |
| 6008 | System | Unerwartetes Herunterfahren (Absturz/Stromausfall) |
| 7036 | System | Dienst gestartet oder gestoppt |
| 1074 | System | Geplanter Neustart (wer hat ihn ausgelöst?) |

---

## Schritt für Schritt: Events effizient nutzen

### Per PowerShell filtern

```powershell
# Letzte 10 Fehler im System-Log:
Get-EventLog -LogName System -EntryType Error -Newest 10 |
    Format-Table TimeGenerated, Source, EventID, Message -Wrap

# Fehlgeschlagene Anmeldungen (letzte 24h):
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4625
    StartTime = (Get-Date).AddHours(-24)
} | Format-Table TimeCreated, Message -Wrap

# Unerwartete Neustarts:
Get-WinEvent -FilterHashtable @{ LogName='System'; Id=6008 } -MaxEvents 5
```

### Benutzerdefinierte Ansicht erstellen

1. Event Viewer → Rechtsklick auf **Benutzerdefinierte Ansichten** → **Benutzerdefinierte Ansicht erstellen**
2. Konfiguration:

| Feld | Wert |
|------|------|
| Zeitraum | Letzte 24 Stunden |
| Ereignisebene | Kritisch + Fehler |
| Protokolle | System, Anwendung |

3. Name: `Kritische Fehler (24h)`

---

## Challenge

!!! question "Challenge: Sicherheits-Audit auswerten"
    1. Finde alle fehlgeschlagenen Anmeldeversuche der letzten 7 Tage (Event-ID 4625)
    2. Finde heraus, wann der Server zuletzt neu gestartet wurde (Event-ID 6005/6006)
    3. Erstelle eine benutzerdefinierte Ansicht "Sicherheitsprobleme" die nur Event-IDs 4625, 4740, 4767 anzeigt
    4. Exportiere die Ergebnisse als CSV

??? success "Hinweis"
    ```powershell
    # Fehlgeschlagene Anmeldungen (7 Tage):
    Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4625; StartTime=(Get-Date).AddDays(-7)
    } | Export-Csv "C:\temp\failed-logins.csv" -NoTypeInformation

    # Letzter Neustart:
    Get-WinEvent -FilterHashtable @{ LogName='System'; Id=6005 } -MaxEvents 1

    # CSV exportieren
    Get-WinEvent -FilterHashtable @{
        LogName='Security'; Id=4625,4740,4767
    } | Select-Object TimeCreated, Id, Message |
        Export-Csv "C:\temp\security-events.csv" -NoTypeInformation
    ```

---

Weiter zu [Modul 40 – Performance Monitor](modul-40-perfmon.md) →
