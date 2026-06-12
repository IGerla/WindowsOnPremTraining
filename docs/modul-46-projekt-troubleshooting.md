# Modul 46 – Projekt: Troubleshooting

## Lernziele

Nach diesem Modul kannst du:

- Systematisch Fehler in einer Windows-Server-Umgebung diagnostizieren
- Die richtigen Tools für verschiedene Problemkategorien einsetzen
- Netzwerk-, Dienst- und Berechtigungsprobleme isolieren
- Unter Zeitdruck strukturiert vorgehen
- Lösungen dokumentieren (für das nächste Mal)

---

## Hintergrund: Troubleshooting – die Königsdisziplin

Im echten Admin-Alltag klingelt das Telefon und jemand sagt: "Das Internet geht nicht" oder "Ich kann mich nicht anmelden" oder "Die Freigabe ist weg". Deine Aufgabe: **schnell und systematisch** die Ursache finden.

### Die Troubleshooting-Methode

```
1. Problem definieren     → Was genau funktioniert nicht? Seit wann?
2. Scope eingrenzen       → Nur ein User? Ein Server? Alle?
3. Hypothese bilden       → Was könnte die Ursache sein?
4. Testen                 → Hypothese prüfen (ping, nslookup, Test-Path...)
5. Lösung implementieren  → Fix anwenden
6. Verifizieren           → Problem wirklich behoben?
7. Dokumentieren          → Was war's? Wie gelöst?
```

### Dein Werkzeugkasten

| Problem-Kategorie | Tools |
|-------------------|-------|
| Netzwerk | `ping`, `tracert`, `nslookup`, `Test-NetConnection`, `ipconfig` |
| DNS | `Resolve-DnsName`, `nslookup`, `Clear-DnsClientCache` |
| Dienste | `Get-Service`, `Restart-Service`, Event Viewer |
| Berechtigungen | `icacls`, `Get-Acl`, `whoami /groups` |
| AD/Anmeldung | `Test-ComputerSecureChannel`, `nltest`, `klist` |
| Freigaben | `Get-SmbShare`, `Get-SmbSession`, `net use` |

---

## Challenge: Die kaputte Umgebung

!!! question "Challenge: Finde und behebe alle Fehler!"
    Dein Trainer hat absichtlich **5 Fehler** in die Umgebung eingebaut. Finde und behebe sie alle!
    
    **Symptome (Tickets von Benutzern):**
    
    1. "Ich kann mich nicht anmelden – Passwort stimmt aber!"
    2. "Die Webseite unseres Intranet-Servers ist nicht erreichbar"
    3. "Ich bekomme keine IP-Adresse mehr automatisch"
    4. "Die Freigabe \\SRV-FS01\Projekte ist nicht erreichbar"
    5. "DNS-Auflösung funktioniert nicht – ich kann keine Servernamen mehr auflösen"
    
    **Für den Trainer – mögliche Fehler einbauen:**
    
    ```powershell
    # Fehler 1: Benutzerkonto sperren
    # 10x falsches Passwort eingeben oder:
    # Set-ADUser testuser -Enabled $false
    
    # Fehler 2: Webserver-Dienst stoppen
    # Stop-Service W3SVC
    
    # Fehler 3: DHCP-Server-Dienst stoppen
    # Stop-Service DHCPServer
    
    # Fehler 4: Freigabe-Berechtigung entfernen
    # Revoke-SmbShareAccess -Name "Projekte" -AccountName "Domänen-Benutzer" -Force
    
    # Fehler 5: DNS-Server-Dienst stoppen oder Forwarder entfernen
    # Stop-Service DNS
    ```

??? success "Hinweis"
    **Systematisches Vorgehen pro Ticket:**
    
    | Ticket | Erster Check | Tool |
    |--------|-------------|------|
    | 1 (Anmeldung) | Konto gesperrt? Deaktiviert? | `Get-ADUser testuser -Properties LockedOut, Enabled` |
    | 2 (Webseite) | Dienst läuft? Port offen? | `Test-NetConnection SRV-APP01 -Port 80` |
    | 3 (Kein DHCP) | DHCP-Dienst läuft? Scope aktiv? | `Get-Service DHCPServer`, `Get-DhcpServerv4Scope` |
    | 4 (Freigabe) | Existiert sie? Berechtigung? | `Get-SmbShare Projekte`, `Get-SmbShareAccess Projekte` |
    | 5 (DNS) | DNS-Dienst läuft? Erreichbar? | `Test-NetConnection DC01 -Port 53` |

---

Weiter zu [Modul 47 – Abschluss & Ausblick](modul-47-abschluss.md) →
