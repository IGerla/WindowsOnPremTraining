# Modul 6 – DHCP

## Lernziele

Nach diesem Modul kannst du:

- Erklären was DHCP (Dynamic Host Configuration Protocol) macht
- Die DHCP-Serverrolle installieren und konfigurieren
- Einen Scope (Adressbereich) erstellen
- Reservierungen für bestimmte Geräte einrichten
- DHCP-Optionen (Gateway, DNS-Server) konfigurieren
- DHCP-Leases prüfen und Probleme diagnostizieren

---

## Hintergrund: DHCP – automatische Hausnummern

Stell dir eine neue Siedlung vor: Jedes Haus braucht eine Hausnummer, damit die Post zugestellt werden kann. Du könntest an jede Tür klopfen und die Nummer per Hand zuweisen – oder du stellst einen Automaten auf, der jedem neuen Bewohner automatisch die nächste freie Nummer gibt.

**DHCP** ist dieser Automat für IP-Adressen:

| Ohne DHCP (statisch) | Mit DHCP (automatisch) |
|---|---|
| Jeder PC manuell konfigurieren | PCs bekommen IP automatisch |
| Fehler bei Tippfehlern | Keine Duplikate möglich |
| Bei 100 PCs = 100x Aufwand | Bei 100 PCs = einmal konfigurieren |

!!! info "Wann statisch, wann DHCP?"
    **Server** bekommen eine feste (statische) IP – sie müssen immer unter derselben Adresse erreichbar sein. **Clients** (PCs, Laptops, Drucker) bekommen ihre IP per DHCP.

---

*Dieses Modul wird noch ausgearbeitet.*

---

Weiter zu [Modul 7 – Aufräumen](modul-7-aufräumen.md) →
