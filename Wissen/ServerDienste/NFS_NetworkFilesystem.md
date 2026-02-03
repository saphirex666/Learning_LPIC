# 📦 NFS – Network File Storage für Linux Server & Homelabs
## Zentrale Storage-Architektur für Docker • AI • Worker-VMs • Cluster

> Ziel: Ein gemeinsamer Speicher für alle Maschinen, der sich wie eine lokale Festplatte anfühlt.

---

# 🧠 Was ist NFS?

## 👉 :contentReference[oaicite:0]{index=0}

**Network File System (NFS)** ist ein Linux/Unix-Protokoll, mit dem Ordner über das Netzwerk freigegeben werden können.

Dadurch können andere Rechner:

- lesen
- schreiben
- Dateien erstellen
- löschen

… als wäre das Laufwerk lokal eingebaut.

---

## 💡 Kurz gesagt

NFS macht:

```
remote Ordner  →  fühlt sich an wie lokal
```

Beispiel:

```bash
mount storage:/data /mnt/data
```

Danach:

```
/mnt/data
```

ist technisch ein Netzlaufwerk – aber Linux merkt keinen Unterschied.

---

---

# 🎯 Warum braucht man NFS?

## Problem ohne NFS

Mehrere VMs/Server → jede speichert lokal:

❌ Daten verteilt  
❌ Backups kompliziert  
❌ doppelte Dateien  
❌ Migration schwierig  
❌ kein Cluster möglich  

---

## Lösung mit NFS

```
alle Worker → 1 Storage VM
```

✅ zentral  
✅ sauber  
✅ skalierbar  
✅ Datacenter-Standard  

---

---

# 🏗 Architekturprinzip

## Typisches Setup

```
                Netzwerk
        ┌─────────────────────┐

   Worker VM      Worker VM      Worker VM
     (n8n)         (AI)          (Scraper)
        │             │              │
        └────────── NFS Client ──────┘
                      │
                 Storage VM
                      │
                     /data
```

---

## Denkweise

| Rolle | Aufgabe |
|--------|-----------|
| Worker | rechnen |
| Storage | speichern |

👉 Trennung von Compute & Storage

Das ist exakt Datacenter-Design.

---

---

# ⚙️ Technische Funktionsweise

NFS arbeitet auf **Kernel-Level**.

Wenn du:

```bash
touch /mnt/data/file.txt
```

passiert intern:

1. Client sendet Dateisystem-Befehl
2. Server schreibt lokal
3. Antwort zurück

Nicht:
- Upload
- FTP
- Kopieren

Sondern echte Filesystem-Operationen (open/read/write).

👉 Deshalb ist es schnell.

---

---

# 🔑 Wichtiger Unterschied zu SMB/Windows

## 👉 :contentReference[oaicite:1]{index=1}

| Feature | NFS | Samba |
|----------|-----------|-----------|
| Linux | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| Windows | ⭐ | ⭐⭐⭐⭐⭐ |
| Geschwindigkeit | höher | niedriger |
| Auth | UID/GID | Username/Passwort |

---

### Best Practice

| Gerät | Empfehlung |
|-----------|--------------|
| Linux Server | NFS |
| Docker | NFS |
| Windows PC | Samba |
| Mac | Samba |

---

---

# 🧬 NFS Besonderheiten (wichtig verstehen!)

## ❗ NFS nutzt KEINE Benutzernamen

Sondern:

👉 UID / GID (Linux IDs)

Beispiel:

```
n8n → UID 1003
```

Der Server vertraut dieser Zahl einfach.

Es gibt:
- kein Login
- kein Passwort
- keine Auth

Sehr Unix-typisch.

---

## Konsequenz

Alle Systeme müssen:

✅ gleiche UIDs haben  
ODER  
✅ root squash deaktivieren  

---

---

# 📁 Grundkonfiguration

## Server

### Installation

```bash
apt install nfs-kernel-server
```

---

### Freigaben

`/etc/exports`

```bash
/data 10.0.0.0/24(rw,sync,no_subtree_check)
```

---

### Aktivieren

```bash
exportfs -ra
systemctl restart nfs-server
```

---

---

## Client

```bash
apt install nfs-common
```

Mount:

```bash
mount storage:/data /mnt/data
```

---

### Dauerhaft (empfohlen)

`/etc/fstab`

```bash
storage:/data /mnt/data nfs rw,_netdev,noatime,vers=4.2 0 0
```

---

---

# ⚡ Performance Tuning

## Empfohlene Optionen

```bash
rw,_netdev,noatime,vers=4.2,rsize=1048576,wsize=1048576,hard,timeo=600
```

---

## Wirkung

| Option | Effekt |
|-----------|----------------|
| vers=4.2 | moderner Standard |
| noatime | weniger IO |
| rsize/wsize | schneller bei großen Dateien |
| hard | keine Datenverluste |
| _netdev | Boot stabil |

---

---

# ✅ Vorteile von NFS

| Vorteil | Warum wichtig |
|------------|------------------------------|
| sehr schnell | Kernel-Level |
| simpel | 1 mount |
| stabil | jahrzehntelang bewährt |
| perfekt für Linux | native |
| ideal für Docker | Volumes einfach |
| zentraler Speicher | Backup leicht |
| Worker austauschbar | Skalierung |

---

---

# ❌ Nachteile von NFS

| Nachteil | Erklärung |
|-------------|-----------------------------|
| keine Benutzer-Auth | nur UID |
| nicht optimal für Windows | SMB besser |
| Netzwerkabhängig | ohne LAN kein Zugriff |
| viele kleine Random Writes → langsamer | nicht DB-ideal |
| kein echtes Cluster-FS | nur 1 Server |

---

---

# 🚫 Wann NICHT NFS?

Nicht geeignet für:

- große Datenbanken (Postgres/MySQL primär lokal)
- WAN/Internet
- 100+ Clients
- Windows-only Umgebungen

Alternativen:
- CephFS
- GlusterFS
- ZFS Replication
- iSCSI

---

---

# 🧱 Empfohlene Ordnerstruktur (Best Practice)

Für Homelab / Docker / AI:

```
/data
├── docker/
│   ├── n8n/
│   ├── redis/
│   ├── db/
├── ai-models/
├── whisper/
├── media/
├── backups/
├── logs/
└── shared/
```

---

---

# 🐳 Docker Integration

Beispiel:

```yaml
volumes:
  - /mnt/data/n8n:/data
```

Container bleibt:
- stateless
- portabel
- austauschbar

---

---

# 📚 LPIC Bezug

## LPIC-1
| Thema |
|------------------------------|
| mount / umount |
| /etc/fstab |
| Dateisysteme |
| Permissions |
| NFS Grundlagen |

---

## LPIC-2
| Thema |
|------------------------------|
| nfs-kernel-server |
| exports |
| Performance |
| Netzwerkdienste |
| Samba vs NFS |

---

## LPIC-3 (Enterprise)
| Thema |
|------------------------------|
| Storage-Design |
| HA Filesysteme |
| Cluster Storage |
| Ceph/Gluster |
| Security Konzepte |
| Datacenter Architektur |

---

👉 NFS ist Kernwissen für ALLE LPIC-Level.

---

---

# 🎯 Warum NFS perfekt für dieses Projekt ist

Für:

- n8n
- Whisper
- AI
- Scraper
- NovaBrain
- Docker Worker
- Cluster

bringt es:

✅ ein Speicher  
✅ einfache Backups  
✅ keine Duplikate  
✅ echte Server-Architektur  
✅ professionelles Setup  

---

---

# 🧠 TL;DR

NFS =

> „eine gemeinsame Netzwerk-Festplatte für Linux“

Freigeben → mounten → benutzen wie lokal.

---

# 🚀 Nächste Schritte

Optional ausbauen mit:

- ZFS Snapshots
- Backup Server
- mehrere Storage Nodes
- CephFS
- Proxmox Storage Pools

---

# 🏁 Fazit

NFS ist:

✔ simpel  
✔ schnell  
✔ robust  
✔ perfekt für Homelabs & Server  

Und der erste Schritt von:

**Bastler → Infrastruktur-Engineer**

---
