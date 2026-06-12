# Modul 24 – Sicherheitsrichtlinien

## Lernziele

Nach diesem Modul kannst du:

- Kontorichtlinien konfigurieren (Passwort-Policy, Account Lockout)
- Überwachungsrichtlinien (Audit Policies) aktivieren
- Benutzerrechte zuweisen und einschränken
- Eingeschränkte Gruppen (Restricted Groups) per GPO steuern
- Software Restriction Policies / AppLocker Grundlagen erklären
- Sicherheitsvorlagen importieren und exportieren

---

## Hintergrund: Sicherheit zentral erzwingen

Ein starkes Passwort hilft nichts, wenn der Benutzer `Passwort1` wählen darf. Sicherheitsrichtlinien **erzwingen** Mindeststandards – ohne auf die Mitarbeit der Benutzer angewiesen zu sein.

### Die drei Säulen der GPO-Sicherheit

| Säule | Was sie schützt | Beispiel |
|-------|----------------|----------|
| **Kontorichtlinien** | Anmeldung | Mindestens 12 Zeichen, Sperrung nach 5 Fehlversuchen |
| **Überwachung (Audit)** | Nachvollziehbarkeit | Wer hat wann auf die Datei zugegriffen? |
| **Benutzerrechte** | Privilegien | Wer darf sich lokal anmelden? Remote zugreifen? |

---

## Kontorichtlinien (Account Policies)

Pfad: **Computerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Sicherheitseinstellungen** → **Kontorichtlinien**

### Kennwortrichtlinien

| Einstellung | Empfehlung | Erklärung |
|-------------|-----------|-----------|
| Minimale Kennwortlänge | 12 Zeichen | Längere Passwörter sind sicherer als komplexe |
| Kennwortkomplexität | Aktiviert | Groß/Klein/Zahl/Sonderzeichen |
| Maximales Kennwortalter | 90 Tage | Erzwingt regelmäßige Änderung |
| Kennwortchronik | 10 | Verhindert Wiederverwendung |
| Minimales Kennwortalter | 1 Tag | Verhindert sofortiges Zurücksetzen |

### Kontosperrungsrichtlinien

| Einstellung | Empfehlung | Erklärung |
|-------------|-----------|-----------|
| Kontosperrungsschwelle | 5 Fehlversuche | Nach 5 falschen Passwörtern → gesperrt |
| Kontosperrdauer | 30 Minuten | Danach automatisch entsperrt |
| Zurücksetzungszähler | 30 Minuten | Fehlerzähler wird nach 30 Min zurückgesetzt |

```powershell
# Aktuelle Passwort-Richtlinie anzeigen:
Get-ADDefaultDomainPasswordPolicy
```

---

## Überwachungsrichtlinien (Audit Policies)

Pfad: **Computerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Sicherheitseinstellungen** → **Erweiterte Überwachungsrichtlinienkonfiguration**

### Wichtige Audit-Kategorien

| Kategorie | Was wird protokolliert |
|-----------|----------------------|
| Anmeldeereignisse | Erfolgreiche und fehlgeschlagene Anmeldungen |
| Objektzugriff | Dateizugriffe (wer öffnete welche Datei?) |
| Kontoverwaltung | Benutzer erstellt/gelöscht/geändert |
| Richtlinienänderungen | GPO-Änderungen |
| Privilegienverwendung | Nutzung von Admin-Rechten |

### Beispiel: Datei-Überwachung aktivieren

1. GPO: Erweiterte Überwachung → **Objektzugriff** → **Dateisystem überwachen** → Erfolg + Fehler
2. Auf dem Ordner: Rechtsklick → Eigenschaften → Sicherheit → Erweitert → **Überwachung**
3. **Hinzufügen** → Wer: "Jeder" → Typ: "Erfolg" → Berechtigung: "Dateien löschen"

```powershell
# Audit-Events im Security Log anzeigen:
Get-EventLog -LogName Security -InstanceId 4663 -Newest 10 |
    Format-Table TimeGenerated, Message -Wrap
```

---

## Eingeschränkte Gruppen (Restricted Groups)

Mit Restricted Groups kontrollierst du per GPO, wer Mitglied in lokalen Gruppen auf den Clients ist. Klassiker: **Wer ist lokaler Administrator?**

Pfad: **Computerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Sicherheitseinstellungen** → **Eingeschränkte Gruppen**

1. Rechtsklick → **Gruppe hinzufügen**
2. Gruppe: `Administratoren` (die lokale Gruppe)
3. **Mitglieder dieser Gruppe**: Nur `Domänen-Admins` und `IT-Support`

!!! warning "Überschreibt lokale Mitgliedschaften!"
    Restricted Groups **ersetzen** alle bestehenden Mitglieder. Wenn du nur "Domänen-Admins" einträgst, werden alle anderen lokalen Admins entfernt. Das ist gewollt – aber sei vorsichtig!

---

## AppLocker (Software-Einschränkung)

AppLocker kontrolliert, **welche Programme** ausgeführt werden dürfen:

| Regeltyp | Beschreibung |
|----------|-------------|
| Pfad | Programme aus bestimmten Ordnern erlauben/sperren |
| Herausgeber | Nur signierte Software von bestimmten Herstellern |
| Hash | Nur exakt diese Datei (per Prüfsumme) |

Pfad: **Computerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Sicherheitseinstellungen** → **Anwendungssteuerungsrichtlinien** → **AppLocker**

```powershell
# AppLocker-Dienst muss laufen:
Set-Service -Name AppIDSvc -StartupType Automatic
Start-Service AppIDSvc
```

---

## Challenge

!!! question "Challenge: Sicherheits-GPO erstellen"
    Erstelle eine GPO `Sicherheit – Clients` mit folgenden Einstellungen:

    1. **Kontosperrung** nach 3 Fehlversuchen für 15 Minuten
    2. **Audit**: Erfolgreiche und fehlgeschlagene Anmeldeereignisse protokollieren
    3. **Eingeschränkte Gruppen**: Lokale Administratoren = nur `Domänen-Admins` + `IT-Support`
    4. **Benutzerrecht**: "Lokal anmelden zulassen" = `Domänen-Benutzer`, `Domänen-Admins`

    Verknüpfe mit der OU `Computer` und teste mit einem fehlgeschlagenen Login.

??? success "Hinweis"
    - Kontosperrung: Denke daran, dass Kontorichtlinien nur auf Domänenebene wirken!
    - Audit: Computerkonfiguration → Richtlinien → Windows-Einstellungen → Sicherheitseinstellungen → Lokale Richtlinien → Überwachungsrichtlinie
    - Eingeschränkte Gruppen: Sicherheitseinstellungen → Eingeschränkte Gruppen
    - Benutzerrechte: Lokale Richtlinien → Zuweisen von Benutzerrechten
    
    Nach dem Test: Prüfe im Event Viewer unter **Windows-Protokolle** → **Sicherheit** ob die fehlgeschlagenen Anmeldungen (Event-ID 4625) protokolliert werden.

---

Weiter zu [Modul 25 – Administrative Vorlagen](modul-25-admx.md) →
