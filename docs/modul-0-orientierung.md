# Modul 0 – Orientierung

## Lernziele

Nach diesem Modul kannst du:

- Den Hyper-V Manager öffnen und dich mit dem zentralen Host verbinden
- Die Begriffe Server, Client, Domain und Virtualisierung erklären
- Eine VM-Konsole öffnen und grundlegende VM-Informationen ablesen
- Den Aufbau der geplanten Serverlandschaft beschreiben

---

## Hintergrund: Was ist eine Serverlandschaft?

Stell dir ein kleines Büro vor: Jeder hat seinen eigenen PC, Dateien liegen lokal, jeder verwaltet sein eigenes Passwort. Das funktioniert bei 3 Leuten – aber bei 50?

Eine **Serverlandschaft** löst genau dieses Problem. Statt dass jeder PC alles selbst macht, gibt es spezialisierte Computer (Server), die zentrale Aufgaben übernehmen:

| Alltags-Vergleich | Server-Rolle | Was er macht |
|-------------------|--------------|--------------|
| Telefonbuch | DNS-Server | Übersetzt Namen in Adressen |
| Hausverwaltung | Domain Controller | Verwaltet Benutzer und Passwörter zentral |
| Postfach-Anlage | DHCP-Server | Vergibt automatisch Netzwerk-Adressen |
| Aktenschrank | Fileserver | Speichert Dateien zentral für alle |
| Empfang / Pforte | Firewall / Gateway | Kontrolliert wer rein und raus darf |

### Server vs. Client

- **Server** = Ein Computer, der Dienste für andere bereitstellt (wartet auf Anfragen)
- **Client** = Ein Computer, der Dienste nutzt (stellt Anfragen)

Dein normaler Büro-PC ist ein Client. Er fragt den Server: "Wo liegt die Datei?", "Ist mein Passwort richtig?", "Welche IP-Adresse bekomme ich?"

### Physisch vs. Virtuell

Früher brauchte man für jeden Server einen eigenen physischen Computer. Heute läuft das anders:

| Früher (physisch) | Heute (virtualisiert) |
|---|---|
| 5 Server = 5 Hardware-Kisten | 5 Server = 5 VMs auf einem Host |
| Teuer, laut, viel Strom | Günstig, leise, flexibel |
| Kaputt = Server weg | Kaputt = Snapshot zurückspielen |

**Virtualisierung** bedeutet: Ein leistungsstarker Computer (der **Host**) simuliert mehrere Computer (die **VMs** – Virtual Machines). Jede VM verhält sich wie ein eigenständiger Server, teilt sich aber die Hardware des Hosts.

---

## Deine Trainingsumgebung

In diesem Training arbeitest du auf einem **zentralen Hyper-V-Host**. Das ist ein leistungsstarker Server, auf dem alle Teilnehmer ihre VMs erstellen.

### Was ist Hyper-V?

Hyper-V ist Microsofts **Virtualisierungstechnologie** – eingebaut in Windows Server und Windows 10/11 Pro. Es ermöglicht dir, beliebig viele virtuelle Server auf einem physischen Host zu betreiben.

### Dein Arbeitsplatz

```
┌──────────────────────────────────────────────────┐
│         Zentraler Hyper-V-Host                   │
│         (steht im Serverraum)                    │
│                                                  │
│  ┌────────┐  ┌────────┐  ┌────────┐            │
│  │ Deine  │  │ Deine  │  │ Deine  │  ...       │
│  │ VM 1   │  │ VM 2   │  │ VM 3   │            │
│  └────────┘  └────────┘  └────────┘            │
└──────────────────────────────────────────────────┘
        ▲
        │ Netzwerk
        ▼
┌──────────────────┐
│  Dein PC         │
│  (Hyper-V Manager│
│   oder RDP)      │
└──────────────────┘
```

Du sitzt an deinem normalen PC und verbindest dich mit dem **Hyper-V Manager** zum zentralen Host. Von dort aus erstellst und verwaltest du deine VMs.

---

## Die wichtigsten Begriffe

Bevor du loslegst, hier die Begriffe die dir immer wieder begegnen:

| Begriff | Erklärung |
|---------|-----------|
| **Host** | Der physische Server, auf dem Hyper-V läuft |
| **VM** (Virtual Machine) | Ein simulierter Computer auf dem Host |
| **Hyper-V Manager** | Das Verwaltungstool für VMs (wie eine Fernbedienung) |
| **Snapshot / Checkpoint** | Ein "Foto" des aktuellen VM-Zustands – zum Zurückspulen bei Fehlern |
| **Virtuelles Switch** | Ein simulierter Netzwerk-Switch, der VMs verbindet |
| **Domain** (Domäne) | Ein Netzwerk mit zentraler Benutzerverwaltung (Active Directory) |
| **Domain Controller (DC)** | Der Server, der die Domäne verwaltet |
| **Active Directory (AD)** | Die Datenbank mit allen Benutzern, Gruppen und Computern |

---

## Schritt für Schritt: Hyper-V Manager verbinden

### Schritt 1: Hyper-V Manager öffnen

1. Drücke auf deinem PC die **Windows-Taste**
2. Tippe `Hyper-V Manager` und drücke **Enter**
3. Das Fenster "Hyper-V-Manager" öffnet sich

!!! info "Hyper-V Manager nicht gefunden?"
    Falls der Hyper-V Manager nicht installiert ist: **Einstellungen** → **Apps** → **Optionale Features** → **Weitere Windows-Features** → Haken bei **Hyper-V-Verwaltungstools** setzen → Neustart.

### Schritt 2: Mit dem Host verbinden

1. Klicke im linken Bereich mit rechts auf **Hyper-V-Manager**
2. Wähle **Verbindung mit dem Server herstellen...**
3. Wähle **Anderer Computer** und trage den Hostnamen ein:

| Feld | Wert |
|------|------|
| Computer | den Hostnamen, den du von deinem Betreuer erhalten hast |

4. Klicke **OK**

Nach wenigen Sekunden erscheint der Host im linken Bereich. Klicke darauf – du siehst jetzt alle VMs auf diesem Host.

!!! tip "Tipp: Zugangsdaten"
    Falls du nach Anmeldedaten gefragt wirst, verwende die Zugangsdaten die dir dein Betreuer gegeben hat. Merke dir: **Benutzername** und **Passwort** notieren!

### Schritt 3: Den Hyper-V Manager kennenlernen

Nach dem Verbinden siehst du drei Bereiche:

| Bereich | Position | Was du dort findest |
|---------|----------|---------------------|
| **Navigation** | Links | Liste der Hosts und VMs |
| **VM-Liste** | Mitte | Alle VMs mit Status (Aus, Wird ausgeführt, Gespeichert) |
| **Aktionen** | Rechts | Befehle (Neu, Einstellungen, Verbinden, Starten...) |

---

## Schritt für Schritt: Eine VM-Konsole öffnen

Falls bereits eine Test-VM existiert (frage deinen Betreuer), kannst du sie jetzt öffnen:

### Schritt 4: VM starten und verbinden

1. Klicke in der **VM-Liste** (Mitte) auf eine VM
2. Falls der Status **Aus** ist: Klicke rechts auf **Starten**
3. Doppelklicke auf die VM (oder klicke rechts auf **Verbinden...**)
4. Ein neues Fenster öffnet sich – das ist die **VM-Konsole** (wie ein Monitor der direkt am Server hängt)

!!! success "Geschafft!"
    Du siehst jetzt den Bildschirm der virtuellen Maschine. Du kannst Maus und Tastatur verwenden wie an einem normalen PC. Mit **Strg+Alt+Links** verlässt du die VM-Konsole wieder.

---

## VM-Informationen ablesen

Wenn du eine VM in der Liste auswählst (einmal klicken, nicht doppelklicken), siehst du unten im mittleren Bereich die **Details**:

| Information | Bedeutung |
|-------------|-----------|
| Status | Aus / Wird ausgeführt / Gespeichert |
| CPU-Auslastung | Wie stark die VM gerade arbeitet |
| Zugewiesener Arbeitsspeicher | Wie viel RAM die VM nutzt |
| Betriebszeit | Wie lange die VM schon läuft |
| Takt | Anzahl der zugewiesenen Prozessorkerne |

### Mit PowerShell (Alternative)

Öffne auf deinem PC eine PowerShell und verbinde dich mit dem Host:

```powershell
Get-VM -ComputerName DEIN-HOSTNAME | Format-Table Name, State, CPUUsage, MemoryAssigned
```

Dieser Befehl zeigt dir alle VMs mit ihrem Status auf einen Blick.

---

## Die geplante Serverlandschaft

Im Lauf dieses Trainings baust du folgende Server auf:

| VM-Name | Rolle | Lernpfad |
|---------|-------|----------|
| `DC01` | Domain Controller, DNS, DHCP | LP 1 |
| `SRV-FS01` | Fileserver, DFS | LP 3 |
| `SRV-APP01` | Webserver (IIS), Anwendungen | LP 2 |
| `CLIENT01` | Windows 10/11 Client (Domänen-Mitglied) | LP 1 |

Jedes Modul bringt dich einen Schritt näher zu dieser fertigen Umgebung.

---

## Challenge

!!! question "Challenge: VM-Steckbrief erstellen"
    Verbinde dich mit dem Hyper-V-Host und finde eine vorhandene VM. Erstelle einen "Steckbrief" mit folgenden Informationen:

    - Name der VM
    - Aktueller Status (An/Aus)
    - Zugewiesener RAM
    - Anzahl Prozessorkerne
    - Welches virtuelle Switch ist angeschlossen?

    Benutze dafür den Hyper-V Manager **oder** PowerShell (`Get-VM` und `Get-VMNetworkAdapter`).

??? success "Hinweis"
    Im Hyper-V Manager: VM auswählen → unten die Details lesen. Für das Netzwerk: Rechtsklick auf die VM → **Einstellungen** → **Netzwerkadapter** → dort steht der Switch-Name.

    Per PowerShell:
    ```powershell
    Get-VM -ComputerName HOSTNAME | Select-Object Name, State, ProcessorCount, MemoryAssigned
    Get-VMNetworkAdapter -ComputerName HOSTNAME -VMName "VM-NAME" | Select-Object SwitchName
    ```

---

Weiter zu [Modul 1 – Erste VM](modul-1-vm.md) →
