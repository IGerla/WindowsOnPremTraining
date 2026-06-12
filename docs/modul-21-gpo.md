# Modul 21 – GPO-Grundlagen

## Lernziele

Nach diesem Modul kannst du:

- Erklären was Group Policy Objects (GPOs) sind und warum sie unverzichtbar sind
- Die Verarbeitungsreihenfolge von GPOs erklären (LSDOU)
- Eine GPO erstellen, bearbeiten und mit einer OU verknüpfen
- Den Unterschied zwischen Computer- und Benutzerkonfiguration erklären
- GPO-Vererbung und -Blockierung verstehen
- Mit `gpresult` und `gpupdate` arbeiten

---

## Hintergrund: Group Policy – eine Regel für alle

Stell dir vor, du hast 200 PCs und auf jedem soll:

- Das Hintergrundbild die Firmenfarben zeigen
- USB-Sticks gesperrt sein
- Das Passwort mindestens 12 Zeichen haben
- Ein bestimmter Drucker installiert sein

Ohne Group Policy: 200 × manuell konfigurieren. Mit Group Policy: **einmal definieren, überall anwenden**.

**Group Policy Objects (GPOs)** sind Regelpakete, die du zentral im Active Directory erstellst und auf Benutzer oder Computer anwendest.

### LSDOU – Verarbeitungsreihenfolge

GPOs werden in dieser Reihenfolge verarbeitet (spätere überschreiben frühere):

```
L – Lokale Richtlinie (auf jedem PC)
S – Site (Standort-GPO, selten genutzt)
D – Domain (Domänen-GPO, gilt für alle)
OU – Organizational Unit (OU-GPOs, am spezifischsten)
```

| Ebene | Beispiel | Priorität |
|-------|----------|-----------|
| Lokal | Lokale Sicherheitsrichtlinie auf dem PC | Niedrigste |
| Site | Standort "Frankfurt" – z.B. Proxy-Einstellungen | ↓ |
| Domain | Domäne "training.local" – z.B. Passwort-Policy | ↓ |
| OU | OU "IT-Abteilung" – z.B. Admin-Tools installieren | Höchste |

!!! tip "Merkhilfe: LSDOU"
    **L**okale → **S**ite → **D**omain → **O**rganizational **U**nit. Die letzte gewinnt!

### Computer- vs. Benutzerkonfiguration

| | Computerkonfiguration | Benutzerkonfiguration |
|---|---|---|
| **Gilt für** | Den Computer (egal wer sich anmeldet) | Den Benutzer (egal an welchem PC) |
| **Beispiele** | Firewall-Regeln, Windows Update, Dienste | Laufwerke mappen, Drucker, Startmenü |
| **Wann angewendet** | Beim Hochfahren | Bei der Anmeldung |

---

## Schritt für Schritt: Erste GPO erstellen

### Schritt 1: Gruppenrichtlinienverwaltung öffnen

```powershell
gpmc.msc
```

Oder: **Server Manager** → **Tools** → **Gruppenrichtlinienverwaltung**

### Schritt 2: Neue GPO erstellen

1. Erweitere **Gesamtstruktur** → **Domänen** → **training.local**
2. Rechtsklick auf **Gruppenrichtlinienobjekte** → **Neu**
3. Name: `Desktop – Firmenhintergrund`
4. Die GPO ist erstellt, aber noch **nicht verknüpft** (wirkt also noch nicht!)

### Schritt 3: GPO bearbeiten

1. Rechtsklick auf die neue GPO → **Bearbeiten**
2. Navigiere zu: **Benutzerkonfiguration** → **Richtlinien** → **Administrative Vorlagen** → **Desktop** → **Desktop**
3. Doppelklick auf **Desktophintergrund**
4. Wähle **Aktiviert**
5. Pfad: `\\training.local\dfs\Vorlagen\wallpaper.jpg`
6. Stil: **Gestreckt**
7. **OK**

### Schritt 4: GPO mit einer OU verknüpfen

1. Zurück in der Gruppenrichtlinienverwaltung
2. Rechtsklick auf die Ziel-OU (z.B. `Benutzer`) → **Vorhandenes Gruppenrichtlinienobjekt verknüpfen...**
3. Wähle `Desktop – Firmenhintergrund` → **OK**

### Schritt 5: GPO testen

Auf einem Client:

```powershell
# GPO-Update erzwingen (statt auf nächsten Neustart zu warten)
gpupdate /force

# Ergebnis prüfen: Welche GPOs wurden angewendet?
gpresult /r

# Detaillierter HTML-Report:
gpresult /h C:\temp\gpo-report.html
Start-Process C:\temp\gpo-report.html
```

!!! success "Geschafft!"
    Nach `gpupdate /force` und Neuanmeldung zeigt der Desktop das neue Hintergrundbild – auf allen PCs in der verknüpften OU.

---

## GPO-Vererbung und Blockierung

### Vererbung deaktivieren

Manchmal soll eine OU **keine** GPOs von übergeordneten OUs erben:

1. Rechtsklick auf die OU → **Vererbung deaktivieren**

!!! warning "Sparsam einsetzen!"
    Vererbungsblockierung macht die GPO-Struktur schnell unübersichtlich. Nutze sie nur, wenn es wirklich nötig ist.

### Erzwingen (Enforced)

Umgekehrt: Eine übergeordnete GPO soll **trotz** Blockierung wirken:

1. Rechtsklick auf die GPO-Verknüpfung → **Erzwungen**

Erzwungene GPOs gewinnen immer – selbst gegen Vererbungsblockierung.

---

## Challenge

!!! question "Challenge: Passwort-Richtlinie per GPO"
    Erstelle eine GPO `Sicherheit – Passwort-Policy` und konfiguriere:

    - Minimale Passwortlänge: 12 Zeichen
    - Passwort-Komplexität: Aktiviert
    - Maximales Passwortalter: 90 Tage
    - Kennwortchronik: 5 Kennwörter gespeichert

    Verknüpfe die GPO mit der Domäne (gilt dann für alle). Prüfe mit `gpresult /r`, ob die Richtlinie angewendet wird.

??? success "Hinweis"
    Pfad in der GPO: **Computerkonfiguration** → **Richtlinien** → **Windows-Einstellungen** → **Sicherheitseinstellungen** → **Kontorichtlinien** → **Kennwortrichtlinien**

    Dort konfigurierst du die vier Einstellungen. Verknüpfe die GPO mit `training.local` (Domänenebene).

    !!! info "Wichtig"
        Kennwortrichtlinien wirken nur, wenn sie auf **Domänenebene** verknüpft sind (nicht auf OU-Ebene). Das ist eine Besonderheit von Kontorichtlinien.

---

Weiter zu [Modul 22 – Desktop-Verwaltung per GPO](modul-22-desktop.md) →
