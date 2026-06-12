# Copilot Instructions – Windows On-Prem Einstiegstraining

Dieses Repository ist eine MkDocs-Material-Dokumentation für ein selbstgeführtes Windows-Server On-Premise Training.
Zielgruppe: IT-Profis im Microsoft-Umfeld (Azubis, Praktikanten, angehende Admins) ohne Server-Erfahrung.
Sprache: **Deutsch** (duzen, direkte Ansprache, keine Fachbegriffe ohne Erklärung).
Zielplattform: **Windows Server 2022** auf einem zentralen Hyper-V-Host.

---

## Projektstruktur

```
mkdocs.yml          ← Navigation, Theme, Plugins
README.md           ← Repo-Übersicht (Modul-Tabelle + Quickstart)
docs/
  index.md          ← Willkommensseite (Tabellen: Was du baust + Modulübersicht)
  trainer-setup.md  ← Anleitung für Trainer (Hyper-V-Host vorbereiten)
  modul-0-*.md      ← Modul-Dateien (fortlaufend nummeriert)
  modul-N-*.md
.github/
  workflows/deploy.yml   ← GitHub Pages Deploy via mkdocs gh-deploy
  copilot-instructions.md
```

---

## Konventionen für neue Module

### 1. Dateiname

```
docs/modul-{N}-{kurzname}.md
```

Beispiel: `docs/modul-3-activedirectory.md`

Keine Umlaute im Dateinamen außer wenn bereits etabliert (Ausnahme: `modul-7-aufräumen.md`).

### 2. Datei-Inhalt (Pflichtstruktur)

```markdown
# Modul {N} – {Titel}

## Lernziele

Nach diesem Modul kannst du:

- ...
- ...

---

## Hintergrund: {Konzept erklären}

Vergleich mit Alltags-Beispielen oder bekannten Konzepten wo sinnvoll.

---

## {Hauptteil: Schritt-für-Schritt}

### Schritt 1: ...
### Schritt 2: ...

---

## Challenge

!!! question "Challenge: ..."
    ...

??? success "Hinweis"
    ...

---

Weiter zu [Modul {N+1} – {Titel}](modul-{N+1}-{kurzname}.md) →
```

### 3. Admonition-Typen (einheitlich verwenden)

| Typ | Wann |
|-----|------|
| `!!! tip` | Nützliche Hinweise, Abkürzungen, PowerShell-Tipps |
| `!!! info` | Hintergrundinformation, Lizenzhinweise |
| `!!! warning` | Wichtige Warnung (Datenverlust, Netzwerk-Unterbrechung) |
| `!!! success` | Erfolgsbestätigung am Ende eines Abschnitts |
| `!!! question` | Challenge-Aufgabe |
| `??? success "Hinweis"` | Aufklappbarer Lösungshinweis zur Challenge |

### 4. Konfigurationstabellen

Immer als Markdown-Tabelle mit `Feld | Wert`:

```markdown
| Feld | Wert |
|------|------|
| VM-Name | `DC01` |
| RAM | 4 GB |
| Netzwerk | `internes Switch` |
```

Ressource-Namen immer in Backticks: `` `DC01` ``, `` `SRV-FS01` ``

---

## Checkliste beim Hinzufügen eines neuen Moduls

Wenn ein neues Modul `modul-{N}-{name}.md` erstellt wird, **immer alle folgenden Dateien aktualisieren**:

### mkdocs.yml – nav-Abschnitt

Neues Modul **vor** dem Aufräumen-Modul des aktuellen Lernpfads einfügen.

### docs/index.md – zwei Stellen

1. Tabelle "Was du in Lernpfad X baust"
2. Tabelle "Übersicht der Module"

### README.md – Modul-Tabelle

Neues Modul einfügen.

### Vorheriges Modul – "Weiter zu"-Link

Am Ende von Modul `{N-1}` den Link anpassen.

### Aufräumen-Modul – Ressourcentabelle

VMs/Ressourcen des neuen Moduls ergänzen.

---

## Stil-Regeln

- **Duzen**: "du kannst", "du siehst", "klicke auf"
- **Alltags-Vergleiche**: Konzepte mit Alltagsbeispielen erklären (DNS = Telefonbuch, DHCP = automatische Hausnummern)
- **PowerShell bevorzugen**: Wo möglich PowerShell-Befehle zeigen (zusätzlich zum GUI-Weg)
- **Screenshots beschreiben**: Keine Screenshots, stattdessen präzise UI-Beschreibungen
- **Keine englischen Begriffe ohne Erklärung**: Erste Verwendung mit Klammer, z.B. "Domain Controller (Domänencontroller)"
- **Trainingsumgebung**: Alle Schritte basieren auf dem zentralen Hyper-V-Host
- **Windows Server 2022**: Alle Anleitungen für diese Version

---

## Themenideen für weitere Lernpfade

| Lernpfad | Module | Thema |
|----------|--------|-------|
| LP 1 | 0–7 | Grundlagen (VM, Grundconfig, AD, Benutzer, DNS, DHCP) |
| LP 2 | 8–14 | Netzwerk & Infrastruktur (VLAN, Routing, Firewall, NPS/RADIUS, VPN) |
| LP 3 | 15–20 | Fileserver & Drucken (Freigaben, DFS, NTFS, Drucker, Quotas) |
| LP 4 | 21–26 | Group Policy (GPO-Grundlagen, Softwareverteilung, Sicherheitsrichtlinien) |
| LP 5 | 27–32 | Zertifikate & PKI (Enterprise CA, Zertifikate, HTTPS) |
| LP 6 | 33–38 | Hochverfügbarkeit (Failover Cluster, Storage Spaces, Hyper-V Replica) |
| LP 7 | 39–43 | Monitoring & Backup (Event Viewer, Performance Monitor, Windows Server Backup) |
| LP 8 | 44–47 | Abschlussprojekte (Komplette Serverlandschaft von Null) |
