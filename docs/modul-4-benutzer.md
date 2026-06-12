# Modul 4 – Benutzer & Gruppen

## Lernziele

Nach diesem Modul kannst du:

- Organizational Units (OUs) als Ordnerstruktur in AD anlegen
- Benutzerkonten erstellen und konfigurieren
- Sicherheitsgruppen erstellen und Benutzer zuweisen
- Den Unterschied zwischen Sicherheits- und Verteilergruppen erklären
- Benutzer per PowerShell anlegen (Massenanlage)

---

## Hintergrund: Ordnung im Active Directory

Active Directory ohne Struktur ist wie ein Aktenschrank ohne Trennblätter – alles liegt in einem großen Haufen. **Organizational Units (OUs)** sind deine Ordner:

```
training.local (Domäne)
├── OU: Benutzer
│   ├── OU: IT-Abteilung
│   ├── OU: Vertrieb
│   └── OU: Geschäftsführung
├── OU: Computer
│   ├── OU: Server
│   └── OU: Clients
└── OU: Gruppen
```

Warum ist das wichtig? Weil du später **Gruppenrichtlinien (GPOs)** auf OUs anwenden kannst – z.B. "Alle Vertriebs-PCs bekommen den Drucker X automatisch installiert."

---

*Dieses Modul wird noch ausgearbeitet.*

---

Weiter zu [Modul 5 – DNS](modul-5-dns.md) →
