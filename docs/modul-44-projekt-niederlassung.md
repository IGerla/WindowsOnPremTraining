# Modul 44 – Projekt: Neue Niederlassung

## Lernziele

Nach diesem Modul kannst du:

- Selbstständig eine komplette Serverinfrastruktur für einen neuen Standort planen
- Alle Schritte aus den Lernpfaden 1–7 ohne Schritt-für-Schritt-Anleitung anwenden
- Einen zusätzlichen Domain Controller mit AD-Replikation einrichten
- Netzwerksegmente mit Routing verbinden
- Deine Arbeit dokumentieren und begründen

---

## Hintergrund: Das Abschlussprojekt

Du hast in den Lernpfaden 1–7 alles gelernt, was du brauchst. Jetzt kommt die Königsdisziplin: **Alles zusammen anwenden – ohne Schritt-für-Schritt-Anleitung.**

### Szenario

Deine Firma eröffnet eine neue Niederlassung. Der Chef sagt:

> "Wir brauchen dort einen Server. Active Directory, Fileserver, DHCP – wie in der Zentrale. Die Mitarbeiter sollen sich mit ihren normalen Accounts anmelden können. Mach mal."

---

## Anforderungen

| Anforderung | Details |
|-------------|---------|
| Standort | "Niederlassung" – eigenes Netzwerk-Segment |
| Domain Controller | Zweiter DC für `training.local` (Ausfallsicherheit) |
| Netzwerk | `192.168.200.0/24` (Niederlassung) |
| Routing | Verbindung zur Zentrale (`192.168.100.0/24`) |
| Fileserver | Lokaler Fileserver mit DFS-Replikation zur Zentrale |
| DHCP | Eigener DHCP-Server für das Niederlassungs-Netzwerk |
| DNS | DNS-Auflösung für beide Standorte |
| Backup | Tägliches Backup des DC und Fileservers |

---

## Dein Plan

Erstelle **zuerst** einen Umsetzungsplan bevor du mit der Implementierung beginnst:

### Planungs-Template

```
1. Welche VMs brauche ich?
   - Name, IP, RAM, Rolle(n)

2. Netzwerk-Design
   - Subnetze, Switches, Routing

3. AD-Design
   - Neue OU-Struktur? Sites & Services?

4. Reihenfolge der Umsetzung
   - Was muss zuerst stehen?

5. Tests
   - Wie prüfe ich, dass alles funktioniert?
```

---

## Challenge

!!! question "Challenge: Neue Niederlassung aufbauen"
    Baue die komplette Infrastruktur für die neue Niederlassung auf. 
    
    **Mindestanforderungen:**
    
    1. Neues Subnetz `192.168.200.0/24` mit eigenem vSwitch
    2. Zusätzlicher Domain Controller `DC02` (192.168.200.10)
    3. AD-Replikation zwischen DC01 und DC02 funktioniert
    4. DHCP-Server für das neue Subnetz
    5. Fileserver mit mindestens einer Freigabe
    6. Routing zwischen beiden Subnetzen
    7. Ein Client im neuen Subnetz kann sich anmelden und auf Freigaben zugreifen
    
    **Bonus:**
    
    - DFS-Replikation zwischen den Fileservern beider Standorte
    - Sites & Services in AD konfiguriert
    - Backup-Plan für den neuen Standort

??? success "Hinweis"
    Empfohlene Reihenfolge:
    
    1. vSwitch und Netzwerk einrichten
    2. VM erstellen, Windows installieren, IP konfigurieren
    3. Routing zwischen den Subnetzen herstellen (oder Router-VM)
    4. DC02 aufsetzen: `Install-ADDSDomainController` (nicht `Install-ADDSForest`!)
    5. AD-Replikation prüfen: `repadmin /replsummary`
    6. DHCP installieren und Scope erstellen
    7. Fileserver-Rolle und Freigabe einrichten
    8. Client im neuen Subnetz testen

---

Weiter zu [Modul 45 – Projekt: Migration](modul-45-projekt-migration.md) →
