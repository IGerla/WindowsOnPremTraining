# Modul 7 – Aufräumen

## Lernziele

Nach diesem Modul kannst du:

- Checkpoints (Snapshots) deiner VMs erstellen
- VMs sauber herunterfahren und stoppen
- Den aktuellen Stand deiner Serverlandschaft zusammenfassen

---

## Was du in Lernpfad 1 gebaut hast

| VM | Rolle | Konfiguration |
|----|-------|---------------|
| `DC01` | Domain Controller | AD DS, DNS, DHCP, feste IP |
| `CLIENT01` | Domänen-Client | Domäne beigetreten, IP per DHCP |

## Checkpoints erstellen

Bevor du Pause machst oder mit Lernpfad 2 weitermachst, sichere den aktuellen Stand deiner VMs:

### Per Hyper-V Manager

1. Rechtsklick auf die VM → **Prüfpunkt** (Checkpoint)
2. Warte bis der Checkpoint erstellt ist (Fortschrittsanzeige unten)
3. Wiederhole für jede VM

### Per PowerShell

```powershell
Checkpoint-VM -Name "DC01" -SnapshotName "LP1-fertig"
Checkpoint-VM -Name "CLIENT01" -SnapshotName "LP1-fertig"
```

!!! warning "Checkpoints sind kein Backup!"
    Checkpoints sind praktisch zum Zurückspulen bei Fehlern, aber sie ersetzen kein echtes Backup. Sie belegen zusätzlichen Speicherplatz und sollten nach einiger Zeit wieder gelöscht werden.

---

## VMs stoppen

Wenn du heute aufhörst:

1. Fahre die VMs **von innen** herunter (Start → Herunterfahren) oder:

```powershell
Stop-VM -Name "DC01" -Force
Stop-VM -Name "CLIENT01" -Force
```

!!! tip "Reihenfolge beim Starten"
    Beim nächsten Mal: **Immer zuerst den Domain Controller starten**, dann die Clients. Ohne DC funktioniert die Domänenanmeldung nicht!

---

## Zusammenfassung Lernpfad 1

Du hast in diesem Lernpfad:

- [x] Den Hyper-V Manager kennengelernt und dich verbunden
- [x] Eine Windows Server 2022 VM erstellt
- [x] Den Server konfiguriert (Name, IP, Updates)
- [x] Active Directory installiert und eine Domäne eingerichtet
- [x] Benutzer, Gruppen und OUs angelegt
- [x] DNS-Zonen und Einträge verstanden
- [x] DHCP für automatische IP-Vergabe konfiguriert

!!! success "Lernpfad 1 abgeschlossen!"
    Glückwunsch! Du hast die Grundlagen einer Windows-Serverlandschaft aufgebaut. Dein `DC01` ist ein vollwertiger Domain Controller mit DNS und DHCP – das Herzstück jedes Windows-Netzwerks.

---

*Weiter geht's mit Lernpfad 2 – Netzwerk & Infrastruktur (in Vorbereitung)*
