# Große Schnittstellen-Übersicht

**Linux & Raspberry Pi – Kommunikation, Möglichkeiten, Verbreitung (LPIC-tauglich)**

***

## Legende (LPIC-relevant)

* **Typ**: Character / Block / Bus / Signal
* **Multi-Device**: Wie viele Geräte realistisch ansprechbar sind
* **Linux-Zugriff**: `/dev`, `/sys`, Tools
* **Verbreitung**: Embedded / Industrial / Consumer
* **Typische Rolle**: Wofür es primär genutzt wird

***

## 🔹 Große Vergleichstabelle
| Schnittstelle       | Typ       | Kommunikation             | Leitungen            | Geschwindigkeit | Multi-Device   | Linux Device                 | Typische Geräte        | Vorteile                       | Nachteile                   | Verbreitung      | LPIC-Relevanz |
| ------------------- | --------- | ------------------------- | -------------------- | --------------- | -------------- | ---------------------------- | ---------------------- | ------------------------------ | --------------------------- | ---------------- | ------------- |
| **UART**            | Character | Asynchron, Punkt-zu-Punkt | TX, RX, GND          | niedrig         | 1:1            | `/dev/ttyS*`, `/dev/ttyAMA*` | GPS, Modem, MCU, Debug | Sehr simpel, robust, bootfähig | Keine Adressierung, langsam | sehr hoch        | ⭐⭐⭐⭐⭐         |
| **GPIO**            | Signal    | Digital I/O               | 1 Pin                | sehr niedrig    | viele Pins     | `/dev/gpiochip*`             | LEDs, Taster, Relais   | Extrem flexibel, direkt        | Keine Protokollintelligenz  | sehr hoch        | ⭐⭐⭐⭐⭐         |
| **I²C**             | Bus       | Synchron, Master/Slave    | SDA, SCL             | niedrig         | 10–50          | `/dev/i2c-*`                 | Sensoren, RTC, EEPROM  | Wenige Pins, Adressen          | Langsam, pull-ups nötig     | sehr hoch        | ⭐⭐⭐⭐⭐         |
| **SPI**             | Bus       | Synchron, Master/Slave    | MOSI, MISO, SCLK, CS | hoch            | 1–n (CS)       | `/dev/spidev*`               | Displays, ADC, Flash   | Sehr schnell, stabil           | Viele Pins, kein Auto-Scan  | hoch             | ⭐⭐⭐⭐          |
| **PWM**             | Signal    | Pulsweitenmodulation      | 1 Pin                | mittel          | mehrere Kanäle | `/sys/class/pwm`             | Motor, LED, Lüfter     | Analoge Steuerung digital      | Kein echtes Analogsignal    | hoch             | ⭐⭐⭐           |
| **USB**             | Bus       | Host/Device               | D+, D-, VCC          | hoch            | 127            | `/dev/bus/usb`               | Storage, NIC, HID      | Plug&Play, schnell             | Komplex, Overhead           | extrem hoch      | ⭐⭐⭐⭐⭐         |
| **Ethernet**        | Netzwerk  | Paketbasiert              | 2–8 Adern            | sehr hoch       | unbegrenzt     | `eth*`                       | LAN, Server, IoT       | Standardisiert, robust         | Kabel, Energiebedarf        | extrem hoch      | ⭐⭐⭐⭐⭐         |
| **WLAN**            | Netzwerk  | Paketbasiert              | Funk                 | hoch            | viele          | `wlan*`                      | IoT, Mobile            | Kabellos, flexibel             | Störungen, Latenz           | extrem hoch      | ⭐⭐⭐⭐          |
| **CSI**             | Video     | MIPI                      | Flachband            | sehr hoch       | 1              | `/dev/video*`                | Kamera                 | Hohe Qualität, low-latency     | Proprietär, kurz            | mittel           | ⭐⭐            |
| **HDMI**            | Video     | Digital                   | HDMI                 | sehr hoch       | 1              | DRM/KMS                      | Display                | Standard, Audio+Video          | Strombedarf                 | sehr hoch        | ⭐⭐            |
| **Audio (I²S)**     | Bus       | Synchron                  | SD, WS, CLK          | hoch            | 1–2            | ALSA                         | DAC, Mic               | Gute Audioqualität             | Spezialisiert               | mittel           | ⭐⭐            |
| **CAN Bus**         | Bus       | Nachrichtenbasiert        | CAN-H/L              | mittel          | 100+           | `can0`                       | Automotive, Industrie  | Robust, fehlertolerant         | Extra Hardware              | hoch (Industrie) | ⭐⭐⭐           |
| **LoRa (UART/SPI)** | Funk      | Paketbasiert              | UART/SPI             | sehr niedrig    | viele          | tty/spi                      | Sensor-Netze           | Reichweite                     | sehr geringe Bandbreite     | wachsend         | ⭐⭐            |

***

## 🔹 Multi-Device-Realität (wichtig für Architektur)


| Schnittstelle | Realistische Anzahl Geräte |
| ------------- | -------------------------- |
| UART          | 1                          |
| GPIO          | 20–40 Pins                 |
| I²C           | 10–50                      |
| SPI           | 1–5                        |
| USB           | bis 127                    |
| Ethernet      | unbegrenzt                 |
| WLAN          | unbegrenzt                 |
| CAN           | 50–100                     |
| LoRa          | tausende Nodes             |

***

## 🔹 Kommunikations-Eignung (Praxis)

| Anwendungsfall     | Beste Schnittstelle |
| ------------------ | ------------------- |
| Boot-Debugging     | UART                |
| Sensor-Netz        | I²C                 |
| High-Speed-Display | SPI                 |
| Aktoren steuern    | GPIO / PWM          |
| Massenspeicher     | USB                 |
| Netzwerk           | Ethernet            |
| Industrie          | CAN                 |
| Kamera             | CSI                 |
| Weitbereich        | LoRa                |

***
***

## 🔹 LPIC-typische Prüfungsanker

* **UART** → serielle Konsole, Rescue
* **I²C** → `i2cdetect`
* **SPI** → Bus vs. Punkt-zu-Punkt
* **GPIO** → Character Devices
* **USB** → udev, Hotplug
* **Ethernet** → iproute2
* **Block vs Character Device**
* **/dev vs /sys**
* **Device Tree Aktivierung**

***

## 🔹 Merksatz (LPIC-Gold)

> **GPIO schaltet, I²C spricht, SPI rennt, UART erklärt, USB verbindet, Ethernet skaliert.**


***

# Architektur-Diagramm

**Linux & Raspberry Pi – Schnittstellen & Datenflüsse**

***

## 1️⃣ Architektur – Textuelle Erklärung (LPIC-relevant)

### Schichtenmodell (von unten nach oben)

### **1. Hardware Layer**

Physische Komponenten:

* CPU / SoC
* Pins (GPIO, UART, SPI, I²C)
* Busse (USB, Ethernet)
* Peripherie (Sensoren, Aktoren, Kamera)

➡️ **Keine Linux-Logik**, nur elektrische Signale

***

### **2. Firmware / Boot Layer**

* **Bootloader** (EEPROM / U-Boot)
* **Device Tree**
  * beschreibt verfügbare Hardware
  * aktiviert Schnittstellen (SPI, I²C, UART)
* `/boot/config.txt`

➡️ **entscheidet, was Linux „sieht“**

***

### **3. Kernel Layer**

* Gerätetreiber:
  * `spi_bcm2835`
  * `i2c_bcm2835`
  * `serial`
  * `usbcore`

* Bus-Subsysteme:
  * GPIO
  * I²C
  * SPI
  * Netlink

➡️ **übersetzt Hardware → Software**

***

### **4. Device Node Layer**

Automatisch erzeugt:

* `/dev/ttyAMA0`
* `/dev/i2c-1`
* `/dev/spidev0.0`
* `/dev/gpiochip0`
* `/dev/video0`

➡️ **Linux-Philosophie: Alles ist eine Datei**

***

### **5. udev Layer**

* Setzt:
  * Berechtigungen
  * Gruppen (`dialout`, `gpio`)
  * Symlinks

➡️ **Hotplug & Security**

***

### **6. User Space / Tools**

CLI-Tools:

* `i2cdetect`
* `gpioset`
* `screen`
* `ip`
* `lsusb`

Libraries:

* `libgpiod`
* `spidev`
* `i2c-dev`

➡️ **Hier arbeitet der Administrator / Entwickler**

***

### **7. Anwendungen**

* Sensor-Reader
* Steuerungssoftware
* Netzwerkdienste
* Monitoring
* KI / Edge-Anwendungen

➡️ **Endnutzen**

***

## 2️⃣ Datenfluss – Beispiel

**Temperatursensor (I²C):**

```bash
Sensor → I²C Bus → Kernel-Treiber → /dev/i2c-1
→ i2c-tools → User-Anwendung
```
LED (GPIO):
```bash
User-App → libgpiod → /dev/gpiochip0
→ Kernel → GPIO-Pin → LED
```
3️⃣ Mermaid-Architekturdiagramm (GitHub-fähig)
```Mermaid
flowchart TD
    HW[Hardware\nSoC, Pins, Busse]
    FW[Firmware / Boot\nEEPROM · Device Tree]
    KERNEL[Linux Kernel\nTreiber & Busse]
    DEVNODES[/dev\nDevice Nodes]
    UDEV[udev\nPermissions & Hotplug]
    USERSPACE[User Space\nCLI & Libraries]
    APPS[Anwendungen\nServices · Sensorik · KI]

    HW --> FW
    FW --> KERNEL
    KERNEL --> DEVNODES
    DEVNODES --> UDEV
    UDEV --> USERSPACE
    USERSPACE --> APPS
```

4️⃣ Schnittstellen-Zuordnung (Architektur-Sicht)
```Mermaid
flowchart LR
    GPIO[GPIO\n/dev/gpiochip0]
    UART[UART\n/dev/ttyAMA0]
    I2C[I²C\n/dev/i2c-1]
    SPI[SPI\n/dev/spidev0.0]
    USB[USB\n/dev/bus/usb]
    NET[Ethernet/WLAN\neth0/wlan0]

    USER[User Space Tools]
    KERNEL2[Kernel Drivers]

    USER --> GPIO --> KERNEL2
    USER --> UART --> KERNEL2
    USER --> I2C --> KERNEL2
    USER --> SPI --> KERNEL2
    USER --> USB --> KERNEL2
    USER --> NET --> KERNEL2
```
## 5️⃣ LPIC-Merksätze (prüfungsrelevant)

* **Device Tree entscheidet, ob Hardware existiert**
* **Kernel stellt Geräte bereit**
* **/dev ist der Zugriffspunkt**
* **udev regelt Rechte**
* **User Space nutzt Tools & Libraries**
* **Keine App spricht direkt mit Hardware**

***

## 6️⃣ Typische LPIC-Prüfungsfrage (mit Denkmodell)

> _„Warum erscheint `/dev/i2c-1` nicht?“_

Antwortstruktur:

1. Device Tree aktiviert?
2. Kernel-Treiber geladen?
3. dmesg prüfen
4. udev aktiv?

***



