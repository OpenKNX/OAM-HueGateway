# OpenKNX Hue Gateway

Das OpenKNX Hue Gateway integriert Philips Hue Geräte über die Hue API v2 in das KNX-System.

## Features

- Bis zu **32 Hue-Kanäle**, konfigurierbar als Licht, Steckdose, Bewegungsmelder, Kontaktsensor oder Taster/Schalter
- Bidirektionale Synchronisation zwischen KNX und Philips Hue (Event-Stream + Polling)
- **Lichtmanager** *(via OFM-LightManager)*: bis zu 16 unabhängige Human Centric Lighting Manager mit konfigurierbaren Stützpunkten, Saison-Profilen (Winter/Sommer) und KNX-Sperrobjekten
  - Per-Kanal-HCL-Sperre für Helligkeit und Farbtemperatur (getrennt oder kombiniert)
  - Vorausschau-KOs (nächste N Minuten) und Tages-Fortschritts-KOs
  - Externe Quellen für Helligkeit und Farbtemperatur (inkl. Lux-Skalierung)
  - Multi-Profil-Selektor (bis zu 4 Profile je Kanal) mit Wochentag-, Saison-, Urlaubs- und Feiertags-Bits
  - Per-Kanal-Slew-Rate (Tag/Nacht) mit interner Astro-Berechnung
- **Szenensteuerung**: bis zu 8 Szenen-Slots je Kanal (Schalten, Dimmen, Farbtemperatur, RGB, Hue-Szene); Recall und Store per KNX DPT 18.001
- **Taster/Schalter**: bis zu 4 Tasten je Kanal; Kurz- und Langdruck frei belegbar (DPT-Typ wählbar: Schalten, Dimmen, Szenennummer, Schritt/Stop, Prozentwert, Temperatur, Byte, Word); Drehregler-Unterstützung (Beta)
- WebUI zum Laden der Hue IDs und Web-Konsole für Diagnose
- mDNS-Service (`openknx-bridge.local`)
- Konfigurierbar über ETS

## Abhängigkeiten

| Modul | Version | Hinweis |
|---|---|---|
| [OFM-HueGatewayModule](https://github.com/OpenKNX/OFM-HueGatewayModule) | ≥ 0.7.0 | Pflicht |
| [OFM-LightManager](https://github.com/OpenKNX/OFM-LightManager) | ≥ 0.3.0 | Pflicht (HCL-Engine) |
| [OFM-Network](https://github.com/OpenKNX/OFM-Network) | ≥ 0.6 | Pflicht |
| [OGM-Common](https://github.com/OpenKNX/OGM-Common) | ≥ 1.8 | Pflicht |

### Unterstützte Gerätetypen

| Typ | Beschreibung |
|---|---|
| **Licht** | Schalten, Dimmen, Farbtemperatur, RGB; bidirektionaler Status |
| **Steckdose** | Schalten; Szenensteuerung (Ein/Aus) |
| **Bewegungsmelder** *(Beta)* | Präsenz, optional Temperatur, Lux, Batterie |
| **Kontaktsensor** | Offen/Geschlossen, optional Sabotage, Batterie |
| **Taster/Schalter** *(Beta)* | Bis zu 4 Tasten, Kurz-/Langdruck, Drehregler |

## Anwenderdokumentation

Die Anwenderdokumentation ist in der [Applikationsbeschreibung des Moduls](../OFM-HueGatewayModule/docs/Applikationsbeschreibung.md) zu finden.

## Installation

Eine vorkompilierte Firmware ist unter [Releases](https://github.com/OpenKNX/OAM-HueGateway/releases) verfügbar. ZIP-Datei herunterladen, entpacken und der enthaltenen Anleitung folgen.

## Hardware

Als Hardware wird OpenKNX-kompatible Hardware mit LAN-Anschluss benötigt.
Die vorkompilierte Firmware unterstützt:

- [REG1-LAN-TP-Base](http://device.openknx.de/REG1-LAN-TP-Base) (ESP32, LAN)

## Lizenz

Diese Software steht unter der [GNU GPL v3](LICENSE).
