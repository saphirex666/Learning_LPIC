### Hardware kaufen
https://www.amazon.de/USB-UART-I2C-SPI-JTAG/dp/B0F66SHVYS/ref=sw_img_d_crh_rh_sbs_2?_encoding=UTF8&pd_rd_i=B0F66SHVYS&pd_rd_w=DlXNV&content-id=amzn1.sym.2f8bf1d9-a96f-44f8-81c2-a89de64360c0&pf_rd_p=2f8bf1d9-a96f-44f8-81c2-a89de64360c0&pf_rd_r=466ENYBXPHM0MTRNPBX9&pd_rd_wg=iiTrv&pd_rd_r=93153e93-8849-4ed3-b596-eb47acab7786

### HArdware 2
https://www.amazon.de/cart/add-to-cart/ref=dp_start-bbf_1_glance

# UART – Linux, LPIC & Recovery Handbook

> **Ziel**: Vollständige, praxisnahe Wissensbasis zu UART für LPIC-Vorbereitung, Systemrettung, Debugging, Forensik und Embedded/Linux-Administration.
>
> **Fokus**: Verstehen, analysieren, dokumentieren – nicht ausnutzen oder umgehen.

---

## Inhaltsübersicht

* 00 – Einführung & Scope
* 01 – Grundlagen UART
* 02 – Tools unter Linux
* 03 – Diagnose & Debugging
* 04 – Hardware- & Board-Patterns
* 05 – Bootloader & Bootprozesse
* 06 – Admin- & Recovery-Use-Cases
* 07 – Playbooks & Checklisten
* 08 – LPIC-Mapping
* 09 – Templates & Vorlagen
* 10 – Gerätebibliothek (Devices)
* Glossar
* Historischer Kontext

---

## 00 – Einführung & Scope

### Was dieses Repository ist

* Fachliche Referenz für UART im Linux- und Embedded-Kontext
* Vorbereitungshilfe für **LPIC-1 / LPIC-2 / LPIC-3**
* Praxisleitfaden für:

  * Systemrettung
  * Boot-Debugging
  * Incident-Analyse
  * Forensik (defensiv)

### Was dieses Repository **nicht** ist

* Keine Exploit- oder Bypass-Anleitungen
* Kein Umgehen von Sicherheitsmechanismen
* Kein "Hacking-Handbuch"

---

## 01 – Grundlagen UART

### Definition

**UART (Universal Asynchronous Receiver/Transmitter)** ist eine serielle, asynchrone Kommunikationsschnittstelle.

### Kerneigenschaften

* Punkt-zu-Punkt
* Asynchron (kein gemeinsamer Takt)
* Minimalhardware

### Typische Signale

* GND – Masse
* TX – Send
* RX – Receive
* VCC – Referenz (meist **nicht anschließen**)

### UART vs. RS232 vs. USB

* UART: TTL-Pegel (3.3V / 5V)
* RS232: ±12V (Pegelwandler nötig)
* USB: komplex, treiberabhängig

---

## 02 – Tools unter Linux

### Serielle Terminalprogramme

* screen
* minicom
* picocom
* tio

### Devices

* /dev/ttyS*
* /dev/ttyUSB*
* /dev/ttyACM*

### Berechtigungen

* Gruppen: dialout, uucp
* udev-Regeln für stabile Namen

### Logging

* Mitschnitt von Sessions
* Zeitstempel
* Reproduzierbarkeit

---

## 03 – Diagnose & Debugging

### Kein Output

* TX/RX vertauscht
* falsche Baudrate
* fehlende Masse
* falscher Pegel

### Unlesbarer Output (Gibberish)

* Baudrate falsch
* Clock-Divider
* Invertierte Logik

### Multimeter-Methode

* GND per Durchgang finden
* TX per Spannungsaktivität identifizieren

---

## 04 – Hardware- & Board-Patterns

### Typische Header

* 3-Pin (GND, TX, RX)
* 4-Pin (+VCC)
* 6-Pin (Kombi-Header)

### Bauformen

* Bestückter Header
* Unbestückte Pads
* Testpunkte

### Herstellerlogik

* Entwicklung
* Produktion (Jigs)
* QA
* RMA

---

## 05 – Bootloader & Bootprozesse

### Boot-Phasen über UART

1. ROM / FSP / SoC Init
2. Bootloader (z. B. U-Boot)
3. Kernel
4. Init / systemd

### Bootlogs lesen

* RAM-Init
* Storage-Init
* Netzwerk
* Panic vs. Hang

### Interaktion (defensiv)

* Bootloader erkennen
* Recovery-Hinweise verstehen

---

## 06 – Admin- & Recovery-Use-Cases

### Typische Szenarien

* NAS bootet nicht
* Update fehlgeschlagen
* Bootloop
* Kein Netzwerk

### Nutzen für Admins

* Ursachenanalyse
* Entscheidungsgrundlage
* Dokumentation

### Incident Response

* Log-Sicherung
* Zeitlinien
* Beweiswert (High-Level)

---

## 07 – Playbooks & Checklisten

### Quickstart: UART in 5 Minuten

* Adapter prüfen
* Pins identifizieren
* Terminal starten
* Log sichern

### Field-Kit

* USB-TTL Adapter
* Jumperkabel
* Multimeter
* ESD-Schutz

### Dokumentation

* Fotos
* Pinout
* Logs

---

## 08 – LPIC-Mapping

### Relevante Themen

* TTY-Konzept
* Device-Files
* Permissions
* systemd-getty (serial)
* Kernel-Logs

### Lernlabs

* Serielle Konsole simulieren
* Logs interpretieren

---

## 09 – Templates & Vorlagen

### DEVICE_PROFILE.md

* Gerät
* Board
* UART-Pins
* Einstellungen

### RECOVERY_LOG.md

* Symptom
* Logauszug
* Ursache
* Lösung

### LAB_NOTES.md

* LPIC-Bezug
* Erkenntnisse

---

## 10 – Gerätebibliothek (Devices)

### Zweck

* Eigene Geräteprofile
* Wiederkehrende Muster
* Wissensaufbau

---

## Glossar

* UART
* TTL
* Baudrate
* 8N1
* Flow Control
* SoL (Serial over LAN)

---

## Historischer Kontext

UART existiert seit den **1960er-Jahren**.

* 1960er: Großrechner & Terminals
* 1978: Intel 8250
* 1980er: UNIX-Standard
* 1990er: PCs & Server
* 2000er: Embedded Linux
* Heute: NAS, Router, IoT, Industrie

**Warum UART überlebt hat**:

* minimal
* robust
* unabhängig
* immer verfügbar

> UART ist das Nervensystem, wenn alles andere versagt.

---

## Abschluss

Dieses Repository ist als **lebendes System** gedacht:

* erweiterbar
* dokumentationsfähig
* monetarisierbar (Training, Recovery, Beratung)
