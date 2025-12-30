## 204.1 RAID konfigurieren

### Theorie
- ...

### Wichtige Befehle
- `mdadm`
- `lsblk`
- `cat /proc/mdstat`

### Beispiele
```bash
mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdb /dev/sdc
