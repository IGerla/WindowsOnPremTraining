# Modul 16 – NTFS-Berechtigungen

## Lernziele

Nach diesem Modul kannst du:

- Die sechs Standard-NTFS-Berechtigungen erklären
- Berechtigungen auf Ordner und Dateien setzen (GUI + PowerShell)
- Vererbung verstehen, deaktivieren und gezielt unterbrechen
- Effektive Berechtigungen eines Benutzers prüfen
- Best Practices für Berechtigungsstrukturen anwenden (AGDLP)

---

## Hintergrund: NTFS – wer darf was?

Stell dir einen Aktenschrank vor: Jede Schublade hat ein Schloss und nur bestimmte Schlüssel passen. NTFS-Berechtigungen sind diese Schlösser für dein Dateisystem.

### Die Standard-Berechtigungen

| Berechtigung | Was sie erlaubt |
|---|---|
| **Vollzugriff** | Alles (inkl. Berechtigungen ändern und Besitz übernehmen) |
| **Ändern** | Lesen + Schreiben + Löschen |
| **Lesen & Ausführen** | Dateien öffnen und Programme starten |
| **Ordnerinhalt auflisten** | Ordnerinhalt sehen (nur auf Ordnern) |
| **Lesen** | Dateien öffnen, aber nicht ändern |
| **Schreiben** | Neue Dateien erstellen, bestehende ändern |

### Vererbung

Berechtigungen werden standardmäßig **vererbt** – von oben nach unten:

```
D:\Freigaben\                    ← IT-Abteilung: Lesen
├── Projekte\                    ← erbt: IT-Abteilung: Lesen
│   ├── Projekt-A\              ← erbt: IT-Abteilung: Lesen
│   │   └── Konzept.docx        ← erbt: IT-Abteilung: Lesen
│   └── Projekt-B\              ← erbt: IT-Abteilung: Lesen
└── Intern\                      ← erbt: IT-Abteilung: Lesen
```

Du kannst die Vererbung an jeder Stelle **unterbrechen**, wenn ein Unterordner andere Berechtigungen braucht.

### AGDLP – Best Practice für Berechtigungen

**A**ccount → **G**lobale Gruppe → **D**omänenlokale Gruppe → **P**ermission (Berechtigung)

```
Benutzer "Max Müller"
  → Mitglied in Globaler Gruppe "IT-Mitarbeiter"
    → Mitglied in Domänenlokaler Gruppe "DL_Projekte_Ändern"
      → Berechtigung "Ändern" auf Ordner "D:\Projekte"
```

Warum so kompliziert? Weil es bei 500 Benutzern und 200 Ordnern die einzige Methode ist, den Überblick zu behalten.

---

## Schritt für Schritt: Berechtigungen setzen

### Schritt 1: Berechtigungen per GUI anzeigen

1. Rechtsklick auf einen Ordner → **Eigenschaften** → Reiter **Sicherheit**
2. Du siehst die Liste der Gruppen/Benutzer und ihre Berechtigungen
3. Klicke auf **Erweitert** für detaillierte Ansicht mit Vererbung

### Schritt 2: Berechtigung hinzufügen (GUI)

1. **Eigenschaften** → **Sicherheit** → **Bearbeiten**
2. **Hinzufügen** → Gruppenname eingeben (z.B. `IT-Abteilung`) → **OK**
3. Berechtigungen anhaken (z.B. **Ändern**)
4. **OK** → **OK**

### Schritt 3: Berechtigungen per PowerShell

```powershell
# Aktuelle Berechtigungen anzeigen
Get-Acl "D:\Freigaben\Projekte" | Format-List

# Berechtigung hinzufügen: IT-Abteilung darf Ändern
$acl = Get-Acl "D:\Freigaben\Projekte"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
    "TRAINING\IT-Abteilung",       # Wer
    "Modify",                       # Was (Ändern)
    "ContainerInherit,ObjectInherit", # Vererbung auf Unterordner + Dateien
    "None",                         # Keine Ausnahmen
    "Allow"                         # Erlauben
)
$acl.AddAccessRule($rule)
Set-Acl "D:\Freigaben\Projekte" $acl
```

### Schritt 4: Vererbung unterbrechen

```powershell
# Vererbung deaktivieren und bestehende Rechte kopieren
$acl = Get-Acl "D:\Freigaben\Projekte\Geheim"
$acl.SetAccessRuleProtection($true, $true)  # (Vererbung stoppen, bestehende Rechte kopieren)
Set-Acl "D:\Freigaben\Projekte\Geheim" $acl

# Jetzt können hier eigene Berechtigungen gesetzt werden,
# unabhängig vom übergeordneten Ordner
```

### Schritt 5: Effektive Berechtigungen prüfen

Was darf ein bestimmter Benutzer **wirklich**? (Kombination aller Gruppen-Mitgliedschaften)

```powershell
# GUI: Eigenschaften → Sicherheit → Erweitert → Effektiver Zugriff
# → Benutzer auswählen → "Effektiven Zugriff anzeigen"

# PowerShell:
Get-Acl "D:\Freigaben\Projekte" | 
    Select-Object -ExpandProperty Access | 
    Where-Object { $_.IdentityReference -like "*IT*" } |
    Format-Table IdentityReference, FileSystemRights, AccessControlType
```

---

## Challenge

!!! question "Challenge: Berechtigungsstruktur nach AGDLP"
    Erstelle folgende Struktur:

    1. Globale Gruppe: `GG_Vertrieb` (Mitglieder: Vertriebs-Benutzer)
    2. Domänenlokale Gruppe: `DL_Vertrieb_Lesen` 
    3. Domänenlokale Gruppe: `DL_Vertrieb_Ändern`
    4. `GG_Vertrieb` → Mitglied in `DL_Vertrieb_Lesen`
    5. Ordner `D:\Freigaben\Vertrieb`: `DL_Vertrieb_Ändern` = Ändern, `DL_Vertrieb_Lesen` = Lesen

    Teste mit einem Vertriebs-Benutzer, ob er lesen aber nicht löschen kann.

??? success "Hinweis"
    ```powershell
    # Gruppen erstellen
    New-ADGroup -Name "GG_Vertrieb" -GroupScope Global -GroupCategory Security
    New-ADGroup -Name "DL_Vertrieb_Lesen" -GroupScope DomainLocal -GroupCategory Security
    New-ADGroup -Name "DL_Vertrieb_Ändern" -GroupScope DomainLocal -GroupCategory Security

    # Verschachtelung
    Add-ADGroupMember -Identity "DL_Vertrieb_Lesen" -Members "GG_Vertrieb"

    # NTFS-Berechtigung setzen
    $acl = Get-Acl "D:\Freigaben\Vertrieb"
    $readRule = New-Object System.Security.AccessControl.FileSystemAccessRule(
        "TRAINING\DL_Vertrieb_Lesen", "ReadAndExecute", 
        "ContainerInherit,ObjectInherit", "None", "Allow")
    $acl.AddAccessRule($readRule)
    Set-Acl "D:\Freigaben\Vertrieb" $acl
    ```

---

Weiter zu [Modul 17 – DFS](modul-17-dfs.md) →
