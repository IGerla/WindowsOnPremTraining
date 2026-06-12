# Modul 23 – Softwareverteilung

## Lernziele

Nach diesem Modul kannst du:

- Software per GPO automatisch auf Clients installieren (MSI-Pakete)
- Den Unterschied zwischen "Zuweisen" und "Veröffentlichen" erklären
- Eine Software-Verteilungsfreigabe einrichten
- MSI-Pakete für die Verteilung vorbereiten
- Installierte Software per GPO wieder deinstallieren
- Grenzen der GPO-Softwareverteilung kennen (und Alternativen benennen)

---

## Hintergrund: Software automatisch verteilen

Stell dir vor, alle 50 PCs in der Firma brauchen eine neue Version von 7-Zip, Notepad++ und dem PDF-Reader. Manuell installieren = 50 × 3 = 150 Installationen. Automatisch per GPO = **einmal konfigurieren, überall installiert**.

### Zuweisen vs. Veröffentlichen

| | Zuweisen (Assign) | Veröffentlichen (Publish) |
|---|---|---|
| **Wo?** | Computer ODER Benutzer | Nur Benutzer |
| **Was passiert?** | Software wird automatisch installiert | Software erscheint in "Programme hinzufügen" |
| **Wer entscheidet?** | Admin (Pflicht-Software) | Benutzer (optionale Software) |
| **Beispiel** | Antivirenprogramm (muss installiert sein) | Bildbearbeitung (wer will, installiert) |

### Voraussetzungen

- Software muss als **MSI-Paket** vorliegen (Windows Installer)
- MSI-Datei liegt auf einer **Netzwerkfreigabe** (erreichbar für Computer und Benutzer)
- EXE-Installer funktionieren NICHT direkt (müssen in MSI umgepackt werden)

---

## Schritt für Schritt: Software per GPO verteilen

### Schritt 1: Software-Freigabe einrichten

```powershell
# Ordner erstellen
New-Item -Path "D:\Software" -ItemType Directory -Force
New-Item -Path "D:\Software\7-Zip" -ItemType Directory -Force
New-Item -Path "D:\Software\Notepad++" -ItemType Directory -Force

# Freigabe erstellen (Nur-Lesen für Computer und Benutzer)
New-SmbShare -Name "Software$" -Path "D:\Software" `
    -ReadAccess "Domänen-Computer","Domänen-Benutzer" `
    -FullAccess "Domänen-Admins"
```

!!! info "MSI-Dateien finden"
    Viele Programme bieten MSI-Downloads an (z.B. 7-Zip: `7z2301-x64.msi`). Lade die MSI in den passenden Ordner auf der Freigabe.

### Schritt 2: GPO erstellen

1. Erstelle eine GPO: `Software – Standardprogramme`
2. Bearbeiten → **Computerkonfiguration** → **Richtlinien** → **Softwareeinstellungen** → **Softwareinstallation**
3. Rechtsklick → **Neu** → **Paket...**
4. Navigiere zur Netzwerkfreigabe: `\\SRV-FS01\Software$\7-Zip\7z2301-x64.msi`

!!! warning "UNC-Pfad verwenden!"
    Wähle die MSI-Datei IMMER über den Netzwerkpfad (`\\Server\Freigabe\...`), niemals über einen lokalen Pfad (`D:\...`). Die Clients müssen die Datei über das Netzwerk erreichen können!

5. Bereitstellungsmethode: **Zugewiesen** (wird automatisch installiert)
6. **OK**

### Schritt 3: GPO verknüpfen und testen

1. Verknüpfe die GPO mit der OU `Computer` (oder `Clients`)
2. Auf dem Client:

```powershell
# GPO aktualisieren
gpupdate /force

# Neustart nötig! (Computerkonfiguration wird beim Hochfahren angewendet)
Restart-Computer
```

Nach dem Neustart ist 7-Zip automatisch installiert.

### Schritt 4: Software deinstallieren

1. In der GPO: Rechtsklick auf das Paket → **Alle Aufgaben** → **Entfernen...**
2. Wähle: **Software bei der nächsten Richtlinienübernahme deinstallieren**
3. Beim nächsten Neustart wird die Software entfernt

---

## Grenzen und Alternativen

| Einschränkung | Alternative |
|---|---|
| Nur MSI-Pakete | SCCM / Intune (auch EXE, Scripts) |
| Keine Fortschrittsanzeige | SCCM Software Center |
| Kein Patch-Management | WSUS (Windows Updates) |
| Langsam bei vielen Paketen | PDQ Deploy, Chocolatey |

!!! tip "Tipp für die Praxis"
    GPO-Softwareverteilung ist gut für kleine Umgebungen (< 100 PCs) und wenige Standardprogramme. In größeren Umgebungen nutzt man **Microsoft Endpoint Configuration Manager (SCCM/MECM)** oder **Intune**.

---

## Challenge

!!! question "Challenge: Zwei Programme verteilen"
    1. Lade zwei MSI-Programme herunter (z.B. 7-Zip und Notepad++) und lege sie in die Freigabe `\\SRV-FS01\Software$`
    2. Erstelle eine GPO die beide als "Zugewiesen" für Computer verteilt
    3. Starte `CLIENT01` neu und prüfe ob beide Programme installiert sind
    4. Entferne eines der Programme wieder per GPO

??? success "Hinweis"
    ```powershell
    # Prüfen ob Software installiert:
    Get-WmiObject -Class Win32_Product | Where-Object { $_.Name -like "*7-Zip*" }
    
    # Oder im Programme-Menü nachschauen
    # Oder: Systemsteuerung → Programme und Features
    ```

    Falls du keine MSI-Dateien hast, kannst du auch eine "leere" MSI zum Testen erstellen oder den 7-Zip MSI-Installer von der offiziellen Webseite nutzen.

---

Weiter zu [Modul 24 – Sicherheitsrichtlinien](modul-24-sicherheit.md) →
