# Changelog

Alle wesentlichen Änderungen am OpenKNX Hue Gateway Applikationsprodukt werden in dieser Datei dokumentiert.

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
