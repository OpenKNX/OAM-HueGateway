# Changelog

Alle wesentlichen Änderungen am OpenKNX Hue Gateway Applikationsprodukt werden in dieser Datei dokumentiert.

## [0.8] - 2026-09-17 — Kanalauswahl nach OpenKNX-Standard

### Geändert (kompatibilitätsrelevant — Breaking in der ETS)
- Mindestversion **OFM-HueGatewayModule 0.8.0** (`%HUE_VerifyVersion%` = 0.8).
- `ApplicationVersion` von `0.7` auf `0.8` erhöht (Dev-Applikation `0.8.0`), Firmware-Revision beginnt damit wieder bei 0.
- **Kanäle werden jetzt auf einer eigenen Seite „Kanalauswahl" aktiviert** (direkt über dem ersten Kanal), mit je einer Zeile aus Kanal, Gerätetyp und Beschreibung. Das Feld „Anzahl aktiver Kanäle" auf der Seite „Allgemein" entfällt.
- **Der Gerätetyp kennt jetzt den Wert `Deaktiviert`.** Nach dem Update steht jeder Kanal auf `Deaktiviert` und muss einmalig neu typisiert werden; alle übrigen Kanaleinstellungen bleiben erhalten.
- Deaktivierte Kanäle erscheinen nicht mehr im Baum links. Beim ersten Öffnen ist kein Kanal aktiv, bisher waren es vier.
- „Kanal deaktivieren (zu Testzwecken)" heißt jetzt **„Suspendiert"** (Nein/Ja) und gilt für alle Gerätetypen, bisher nur für Licht. Suspendierte Kanäle sind im Baum mit ⛔ markiert.
- „Bezeichnung" heißt jetzt **„Beschreibung"** (OpenKNX-Glossar).
- `ReplacesVersions` ergänzt und korrigiert: Die Dev-Liste enthält jetzt auch `0.7.2`, in der Release-Liste war der erste Eintrag durch einen Tippfehler unbrauchbar (`0.70.6` statt `0.7 0.6`) — Geräte dieser Versionen lassen sich damit wieder aktualisieren.

### Keine KO-Verschiebung
- KO-Nummern, Blockgröße und Offsets sind unverändert (`HUE_KoOffset`, `HUE_KoBlockSize`). **Bestehende Gruppenadressen bleiben verknüpft**, anders als beim Update auf 0.7.

### Hinzugefügt (über Modulversion, siehe dortiges CHANGELOG für Details)
- Der Kanaltyp lässt sich sowohl in der Kanalauswahl als auch im Kanalkopf ändern; beide Felder werden synchron gehalten.
- Der VS-Code-Task zum Erzeugen der ETS-Kontexthilfe ist im Modul jetzt mitversioniert.

### Migration
- Vor dem ETS-Download: Backup der bestehenden Projektierung erstellen.
- Nach dem Download in der ETS auf der Seite **Kanalauswahl** für jeden zuvor genutzten Kanal den Gerätetyp erneut wählen. Ziel-ID, Lampentyp, Szenen, Taster- und Lichtmanager-Einstellungen sind danach wieder wirksam.
- Bei Licht-Kanälen mit Rückfallstrategie „Freie Dauer" oder „Freie Uhrzeit": **Freie Rückfalldauer** und **Rückfall-Uhrzeit** prüfen, sie stehen nach dem Update auf dem Standard (1800 s bzw. 3:00 Uhr).
- Neu: Ein/Aus-Lampen haben wieder das KO „Status Schalten"; bei Bedarf eine Gruppenadresse verknüpfen.

## Firmware-Revision 4 (ApplicationVersion weiterhin 0.7) - 2026-09-16 — Performance-Update Bridge-Kommunikation

### Geändert
- Mindestversion **OFM-HueGatewayModule 0.7.4**. Kein Breaking Change, `ApplicationVersion` bleibt bewusst bei `0.7` — keine KO-Verschiebung, keine Neuverknüpfung in der ETS nötig.

### Behoben (über Modulversion, siehe dortiges CHANGELOG für Details)
- Schaltbefehle an die Hue-Bridge liefen bislang seriell und ohne Wiederverwendung der TLS-Verbindung — jeder einzelne Befehl kostete einen vollen Handshake (~900 ms–1 s). Mehrere Lampen kurz hintereinander geschaltet summierten sich dadurch auf mehrere Sekunden sichtbarer Verzögerung.
- Der EventStream (Push-Updates von der Bridge) verband sich auf dieser Hardware praktisch nie dauerhaft, weil ihm der interne Arbeitsspeicher für den TLS-Kontext fehlte; das Gateway fiel stattdessen dauerhaft auf sekündliches Abfragen der Bridge zurück.
- Ein Schaltbefehl konnte mehrere Status-Telegramme auf den KNX-Bus bringen, auch wenn sich nur ein einzelner Wert (z. B. nur der Schaltzustand) geändert hatte.

## [0.7] - 2026-05-22 — HCL-Profil-Release

### Geändert (kompatibilitätsrelevant — Breaking)
- Mindestversionen der eingebundenen Module:
  - **OFM-LightManager 0.3.0** (ProfileV2-HCL-Engine, Variante-E-Dispatch, neue KOs K09..K21)
  - **OFM-HueGatewayModule 0.7.0** (Partial-Sink-Integration via `ILightManagerOutput::onLightManagerPartial`)
- **KO-Block des Lichtmanagers wächst von 12 auf 22 KOs je Kanal** (`LMG_KoBlockSize=22`).
  - Alle nachfolgenden HUE-KO-Nummern verschieben sich um **+160** (16 Kanäle × 10 neue KOs).
  - `HUE_KoOffset` wird von OpenKNXproducer aus dem neuen `LMG_KoBlockSize` neu berechnet.
  - **Alle HUE-Gruppenadressen müssen in der ETS nach dem Update neu verknüpft werden.**
- `ApplicationVersion` in `HueGateway.conf.xml` von `0.6` auf `0.7` erhöht.

### Hinzugefügt (über Modulversionen)
- HCL-Per-Kanal-Sperre (K09 LockColor / K10 LockBrightness) mit 3-Wege-Modus (Aus / Vollsperre / Getrennt) — OFM-LightManager 0.3.0.
- HCL-Vorausschau-KOs K11 PreviewMinutes / K12 PreviewColorTemp / K13 PreviewBrightness und Tages-Fortschritts-KOs K14 DayProgress / K15 DayPhase.
- Externe Quellen für Helligkeit und Farbtemperatur (K16..K19) mit Per-SP-Mix und L6 Skalar/Lux-Konvertierung.
- Per-Kanal-Slew Day/Night mit interner Astro-Berechnung als Tag/Nacht-Quelle.
- Multi-Profil-Selektor (bis 4 Profile/Kanal) mit Wochentag-/Saison-/Urlaub-/Feiertag-Bits und Default-Fallback.
- F11 Partial-Sink-Vertrag: HCL-Engine sendet Achsen selektiv mit `validMask`; Hue-Bridge füllt nicht-valide Achsen aus dem letzten Cache auf — verhindert flackernde Hue-Sends bei Achsen-Locks.
- NVS-Magic 0x03 für Per-Channel Summer-State-Persistenz.

### Migration
- Vor ETS-Download: Backup der bestehenden Projektierung erstellen.
- Nach ETS-Download: alle HUE-Gruppenadressen prüfen und neu verknüpfen (KO-Verschiebung +160).
- Details und vollständige Migrations-Hinweise im OFM-LightManager CHANGELOG 0.3.0 sowie in den Help-Baggages `LMG-Migration-0.3.0`.

## [0.6] - vorherige Version
- Siehe Modul-CHANGELOGs `OFM-HueGatewayModule` und `OFM-LightManager` für historische Details.
