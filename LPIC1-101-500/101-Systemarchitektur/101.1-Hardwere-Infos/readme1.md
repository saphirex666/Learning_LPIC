Markdown

````
# LPIC-1 (101.1) – Hardware & Kernelmodule (Spickzettel)

> **Fokus**: Hardware erkennen, Treiber/Kernelmodule prüfen, zentrale Pseudo-FS-Pfade verstehen  
> **LPI-Ziel**: LPIC-1 (101) Thema 101 / Ziel 101.1 – Determine and configure hardware settings

---

## 1. Hardware finden (schnell)

### PCI-Geräte (intern: Controller, NICs, GPUs, etc.)
```bash
lspci                  # Alle PCI-Geräte auflisten
lspci -v               # Mehr Details
lspci -s 04:02.0 -v    # Spezifisches Gerät (Adresse aus lspci)
lspci -s 01:00.0 -k    # Kernel-Treiber / mögliche Module anzeigen
````

**Merke**:

* kernel driver in use: = aktuell gebundener Treiber
* kernel modules: = mögliche Module für das Gerät

### USB-Geräte (Sticks, Maus, BT-Dongle, etc.)

Bash

```
lsusb                  # Alle USB-Geräte
lsusb -v               # Verbose Ausgabe
lsusb -v -d 1781:0c9f  # Spezifisches Gerät (Vendor:Product-ID)
lsusb -t               # Topologie als Baum (Hubs + Treiber + Geschwindigkeit)
lsusb -s 01:20         # Gerät nach Bus:Device-Nummer
```

## 2. Kernelmodule / Treiberverwaltung (kmod-Workflow)

### Geladene Module anzeigen

Bash

```
lsmod                  # Alle geladenen Module
lsmod | grep -i snd_hda_intel   # Nach Modul filtern
```

**Spaltenbedeutung**:

* **Module** – Modulname
* **Size** – Belegter RAM
* **Used by** – Abhängige/benutzende Module (und Referenzzähler)

### Module laden / entladen



```bash 
sudo modprobe nouveau          # Modul laden
sudo modprobe -r snd_hda_intel # Modul entladen (inkl. Abhängigkeiten, wenn möglich)
```

⚠️ Entladen scheitert oft mit „in use“, wenn ein Prozess das Modul nutzt.

### Modul-Infos & Parameter


```bash
modinfo nouveau                # Alle Infos zum Modul
modinfo -p nouveau             # Nur verfügbare Parameter
```

### Modul-Parameter dauerhaft setzen

Datei anlegen (empfohlen): /etc/modprobe.d/nouveau.conf

conf

`options nouveau modeset=0`

### Modul blacklisten (automatisches Laden verhindern)

In /etc/modprobe.d/nouveau.conf (oder eigene Datei):

conf

`blacklist nouveau`

## 3. System-Pfade (Hardwarebezug)

### /proc (Kernel- & Laufzeitinfos – Pseudo-FS)

```bash
cat /proc/cpuinfo      # CPU-Details
cat /proc/interrupts   # IRQ-Zuweisungen
cat /proc/ioports      # I/O-Portbereiche
cat /proc/dma          # DMA-Kanäle
```

### /sys (SysFS – Gerätebaum & Kernel-Hardwaremodell)

* Wird von udev genutzt für Geräteerkennung und Regelanwendung
* Enthält detaillierte Geräte- und Treiberinformationen

### /dev (Gerätedateien)

* Dynamisch von udev erstellt

* Typische Blockdevices:

  * SATA/SCSI/USB: /dev/sda, /dev/sda1, …
  * NVMe: /dev/nvme0n1, /dev/nvme0n1p1, …
  * SD-Karte: /dev/mmcblk0, /dev/mmcblk0p1, …

## 4. Mini-Diagnose-Checkliste (LPIC-Style)

Gerät funktioniert nicht → prüfe in dieser Reihenfolge:

1. **Wird die Hardware erkannt?** → PCI: lspci → USB: lsusb
2. **Ist ein Treiber gebunden?** → lspci -s \<addr> -k
3. **Ist das Modul geladen?** → lsmod | grep \<modul>
4. **Welche Parameter hat das Modul?** → modinfo -p \<modul>
5. **Stört ein falsches Modul?** → Blacklist in /etc/modprobe.d/\<modul>.conf anlegen

## 5. Typische Prüfungsanker (Merksätze)

* lspci = PCI-Bus-Geräte (mit -k Treiberbindung prüfen)
* lsusb = USB-Geräte (mit -t Topologie + Treiber sehen)
* lsmod = aktuell geladene Kernelmodule
* modprobe -r = Modul entladen (scheitert bei „in use“)
* /proc & /sys = Pseudo-Dateisysteme mit Live-Infos aus dem Kernel
* /dev = Gerätedateien (dynamisch von udev erstellt)

Viel Erfolg bei der LPIC-1 Prüfung – diese Befehle sitzen in 101.1 garantiert! 🚀

text

2,2

