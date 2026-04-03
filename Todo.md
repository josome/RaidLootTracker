# RaidLootTracker – Todo

## Offen

### Spieler-Tab: Aufklappbare Loot-Historie

- [ ] **Expandable Player Rows**
  Jede Zeile im Spieler-Tab aufklappbar. Beim Aufklappen erscheinen alle erhaltenen Items
  des aktuellen Raids als eingerückte Sub-Rows (Item, Kategorie, Schwierigkeitsgrad, Prio).
  - Toggle-Button `▶`/`▼` links vom Namen (16px), restliche Spalten bleiben unverändert
  - Expansion-State in `local expandedPlayers = {}` (file-local, überlebt Refresh, nicht Reload)
  - Sub-Rows aus `currentRaid.lootLog` gefiltert nach `GL.ShortName(fullName)`
  - Sub-Rows landen in `playerRows` → automatisch aufgeräumt beim nächsten Refresh
  - Scroll-Höhe passt sich automatisch an (gleicher `yOff`-Counter)
  - Zebra-Stripe-Fix: separater `zebraIdx`-Zähler statt `#playerRows % 2`
  - Nur `UI_SpielerTab.lua` betroffen, keine anderen Dateien

- [ ] **Loot-Historie aus alten Raids anzeigen**
  Aufgeklappte Spieler-Rows zeigen zusätzlich Loot aus `raidHistory` (abgeschlossene Raids),
  gruppiert nach Raid mit Separator-Header (Raid-Name + Datum).
  - Scan über alle `raidHistory[i].lootLog` nach `entry.player == GL.ShortName(fullName)`
  - `itemID` überlebt SavedVariables-Reload → WoWhead-Links funktionieren auch für alte Einträge
  - Aktueller Raid zuerst, danach ältere Raids chronologisch absteigend
  - Optional: Limit auf letzte N Raids oder letzte X Items


- [x] **Falls jemand ausversehen eine prio geschrieben hat soll der ML die weg X en können**
- [x] **Die Settings des Raids sollten mit übertragen werden, relevant sind hier Loot Seltenheit,Timers **
- [ ] **Bei lootverteilung mit prio 4 sollen die bervorzugt werden die das item tragen können**
        Platte aber nur platte und nicht auch stoff, leder und schwere rüstungso 
- [ ] **wenn zwei mal das identische Item droppt sollten beide (oder mehr) Items mit einem gang verrollt werden können
- ~~**Abgleich Discord Raid anmeldungen mit eingeladenen personen**~~ — verworfen (Discord-Namen ≠ WoW-Namen)
- [x] Prios ändern auf 1 BIS 2 OS 4 Transmog
- [] NEW **Wenn mehrere das addon haben, wenn ml nen raid startet, könnte es evtl ne notification bei den anderen geben die gesyncd sind/oder das Fenster öffnet sich**

## Bugs
- [x] ein client der später beitritt bekomme die Raiddaten nicht
- [x] Omni Settoken werden nicht berücksichtigt
- [ ] Soll in dungeons und tiefen nicht funktionieren
---


### CSV Roundtrip: Google Sheets ↔ Addon

- [ ] **CSV um `ItemID` und `Quality` erweitern**
  Aktuelle Spalten: `RaidID, Tier, Difficulty, Track, Date, Status, Player, Item, Category, Prio, Timestamp`
  Erweitert: `RaidID, Tier, Difficulty, Track, Date, Status, Player, Item, ItemID, Quality, Category, Prio, Timestamp`
  → `GL.ExportCSV()` in `Util.lua` anpassen

- [ ] **CSV-Importer im Addon**
  Paste-Feld im Addon (eigener Tab oder im Raid-Tab) für CSV-String aus Google Sheets.
  Parser gruppiert flache Zeilen nach `RaidID` und rekonstruiert `raidHistory`-Einträge
  mit `lootLog` (Status=Assigned) und `trashedLoot` (Status=Trashed).

  **Validierung & Fehlerbehandlung:**
  - Import läuft in temporären Buffer — erst nach vollständiger Validierung committed
  - Pflichtfeld-Check pro Zeile: `RaidID`, `Player`, `Timestamp` (Zahl), `Status` (Assigned/Trashed)
  - Fehlerhafte Zeilen werden übersprungen (Warning), kein kompletter Abbruch
  - Duplikat-Check: falls Raid mit gleicher `RaidID` bereits in `raidHistory` → Confirmation-Dialog
  - Dry-Run Vorschau vor Import: "X Raids, Y Einträge gefunden, Z Zeilen übersprungen"

  **Robustheit gegen Sheets-Eigenheiten:**
  - BOM-Zeichen (`\xEF\xBB\xBF`) am Anfang entfernen
  - `\r\n` und `\n` beide akzeptieren
  - RFC-4180 Quotes behandeln (mehrzeilige Felder, escaped Quotes)
  - Leere Abschlusszeilen ignorieren

---

### Sonstiges

- [ ] **Lokalisierung (i18n)**
  Aktuell ~40+ gestreute Strings, gemischt Deutsch/Englisch, kein L[]-System.
  → `Locales/deDE.lua` + `Locales/enUS.lua` anlegen, alle UI-Strings durch `L["key"]` ersetzen, TOC um Locale-Dateien erweitern.

- [ ] **JSON-Export-Fenster scrollbar machen**
  Das Export-Textfeld ist aktuell nicht scrollbar, der Inhalt geht über das Fenster hinaus.
  → ScrollFrame um das Export-TextBox ergänzen, oder EditBox mit `SetMultiLine(true)` + Scroll-Wrapper

## Erledigt (diese Session)

- [x] Results-Liste zeigt alle Prio-Spieler sortiert (nicht nur höchste Prio)
- [x] Test-Simulation `/rlt testroll` mit 7 Spielern (4x Prio1, 2x Prio2, 1x Prio3)
- [x] ScrollFrame-Bug behoben (UIPanelScrollFrameTemplate braucht benannte Frames)
- [x] Session Loot Bereich vergrößert (~6 Zeilen)
- [x] Duplikat-Button "Roll Now" entfernt
- [x] Fenster-Mindestgröße beim Laden erzwungen (X-Buttons nicht mehr verdeckt)
- [x] Absturz-Warnung in Raid-Statuszeile
- [x] pendingLoot wird bei CloseRaid im Snapshot gespeichert
- [x] pendingLoot wird bei ResumeRaid aus Snapshot wiederhergestellt

