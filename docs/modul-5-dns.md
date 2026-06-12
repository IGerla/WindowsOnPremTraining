# Modul 5 – DNS

## Lernziele

Nach diesem Modul kannst du:

- Erklären was DNS (Domain Name System) macht und warum es wichtig ist
- Forward- und Reverse-Lookup-Zonen unterscheiden
- DNS-Einträge (A, CNAME, PTR, MX) erklären
- Die DNS-Konfiguration auf deinem Domain Controller prüfen
- Neue DNS-Einträge manuell erstellen
- DNS-Probleme mit `nslookup` und `Resolve-DnsName` diagnostizieren

---

## Hintergrund: DNS – das Telefonbuch des Netzwerks

Stell dir vor, du willst einen Freund anrufen. Du kennst seinen Namen, aber nicht seine Nummer. Also schlägst du im Telefonbuch nach: **Name → Nummer**.

DNS funktioniert genauso für Computer: **Computername → IP-Adresse**.

| Ohne DNS | Mit DNS |
|----------|---------|
| `\\192.168.1.10\Freigabe` | `\\SRV-FS01\Freigabe` |
| Wer merkt sich das? | Das merkt sich jeder! |

Active Directory **braucht** DNS zwingend. Ohne DNS finden die Clients den Domain Controller nicht und können sich nicht anmelden.

---

*Dieses Modul wird noch ausgearbeitet.*

---

Weiter zu [Modul 6 – DHCP](modul-6-dhcp.md) →
