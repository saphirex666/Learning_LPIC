# 🔐 SSL / TLS – komplett erklärt (einfach + technisch)

***

# 🧠 1. Was ist SSL überhaupt?

👉 SSL (heute eigentlich **TLS**) sorgt dafür, dass:

* 🔒 Daten **verschlüsselt** übertragen werden
* 🪪 die Website **echt ist**
* 👀 niemand mitlesen kann

👉 Kurz:\
**„HTTPS = sicher + überprüfbar“**

***

# 🍺🔐 2. Kurz erklärt: SSL wie beim Bier kaufen

### 🛒 Die Situation im Einkaufsladen: Du willst  Bier kaufen 

👉 Du gehst mit einem Kasten Bier zur Kasse\
👉 Der Verkäufer muss prüfen: **Bist du 18?**

***

👉 Übertragen auf das Internet:

* 🌐 **Browser = Verkäufer**
* 💻 **Website = Kunde**
* 🪪 **Zertifikat = Ausweis**

***

👉 Ablauf:

Die Website zeigt ihren „Ausweis“ (Zertifikat)\
Der Browser prüft:

* Ist der Ausweis echt?
* Wurde er von einer vertrauenswürdigen Stelle ausgestellt?
* Gehört er wirklich zu dieser Website?

***

👉 Ziel:

**Der Browser entscheidet: Kann ich dieser Website vertrauen?**

***

👉 Ergebnis:

* ✅ Alles passt → 🔒 Verbindung sicher (HTTPS)
* ❌ Zweifel → ⚠️ „Verbindung nicht sicher“


***

# 🧾 3. Die Vertrauenskette (das Wichtigste!)

```bash
🏛️ Root (Staat)
   ↓
🏢 Zwischenzertifikat (Bürgeramt)
   ↓
🪪 Server-Zertifikat (Ausweis der Website)
   ↓
🍺 Browser (Verkäufer prüft)
```
👉 Der Browser prüft IMMER die komplette Kette

***

# 🔑 4. Was steckt technisch dahinter?

## 🔐 4.1 Private Key (geheim!)

* bleibt auf dem Server
* wie deine Unterschrift

***

## 📄 4.2 CSR (Certificate Signing Request)

* Antrag auf Zertifikat  

* enthält:
  * Domain (z. B. `example.com`)
  * Public Key

👉 Vergleich:\
„Bitte stellt mir einen Ausweis aus“

***

## 🏢 4.3 Zertifizierungsstelle (CA)

* z. B. Let’s Encrypt
* prüft:
  * gehört dir die Domain?

👉 Vergleich:\
Bürgeramt prüft dich

***

## 🪪 4.4 Zertifikat

* bestätigt:
  * „Diese Website ist echt“
    
* enthält:
  * Domain
  * Gültigkeit
  * Signatur

***

# 🌐 5. Was passiert beim Aufruf einer Website?

## Schritt für Schritt:

1. Browser verbindet sich mit Server
2. Server schickt:
   * Zertifikat
   * Zwischenzertifikat
3. Browser prüft:
   * stimmt die Domain?
   * ist es gültig?
   * ist die Kette vertrauenswürdig?
4. Wenn alles passt:\
   👉 🔒 Verbindung wird aufgebaut (TLS Handshake)

***

# 🔄 6. TLS Handshake (vereinfacht)

👉 Ziel: **geheimer Schlüssel für Verbindung**

1. Browser sagt: „Hallo, ich will sicher sprechen“
2. Server zeigt Zertifikat
3. Browser prüft Vertrauen
4. Beide einigen sich auf Schlüssel
5. 🔒 Kommunikation startet

***

# ❌ 7. Die 3 wichtigsten Fehler

***

## ❌ Fall 1 – Selbst gedruckter Ausweis

🖨️ Du hast deinen Ausweis selbst gedruckt

```bash
🏛️ Staat ✔️
   ↓
🏢 Bürgeramt ❌ (fehlt komplett)
   ↓
🪪 Ausweis ❌ (selbst gemacht)
   ↓
🍺 Verkäufer: „Das akzeptiere ich nicht“
```
👉 Problem:

* Keine echte Behörde dahinter
* Keine Prüfbarkeit

👉 IT:

* ❌ Self-Signed Zertifikat
* ❌ Zertifizierungsstelle fehlt


***

## ❌ Fall 2 – DDR-Ausweis

🧾 Du zeigst deinen alten DDR-Ausweis

```bash
🏛️ Staat ✔️
   ↓
🏢 Bürgeramt ❌ (nicht mehr gültig)
   ↓
🪪 Ausweis ✔️ (formal echt)
   ↓
🍺 Verkäufer: „Nicht mehr gültig“
```
👉 Problem:

* Ausweis sieht korrekt aus
* Aber die ausstellende Stelle ist nicht mehr vertrauenswürdig
  
👉 IT:
* ❌ Zwischenzertifikat abgelaufen / ungültig


***

## ❌ Fall 3 – Ausweis vom Vater
### 👨 Du bist unter 18 und nimmst den Ausweis deines Vaters

```bash
🏛️ Staat ✔️
   ↓
🏢 Bürgeramt ✔️
   ↓
🪪 Ausweis ✔️ (echt)
   ↓
🍺 Verkäufer: „Das bist nicht du“
```
👉 Problem:

* Alles ist gültig
* Aber der Ausweis gehört NICHT zu dir

👉 IT:

* ❌ Zertifikat passt nicht zur Domain\
  (z. B. Zertifikat für `example.com`, aber Website ist `meineseite.de`)


***

# ✅ 8. Wann ist alles korrekt?

```bash
🏛️ Root ✔️
   ↓
🏢 Zwischenzertifikat ✔️
   ↓
🪪 Zertifikat ✔️
   ↓
🌐 Domain passt ✔️
```
👉 Browser:\
🔒 „Sicher“

***

# 💡 9. Warum wird das Zwischenzertifikat mitgeschickt?

👉 Weil der Browser sonst sagt:

„Ich sehe den Ausweis…\
aber ich weiß nicht, wer ihn ausgestellt hat“

➡️ Deshalb:

* Server sendet komplette Kette

***

# 🔐 10. Was bringt SSL konkret?

## Sicherheit:

* 🔒 Verschlüsselung (kein Mitlesen)
* 🛡️ Schutz vor Manipulation

## Vertrauen:

* 🪪 Website ist echt
* 🚫 Schutz vor Fake-Seiten

***

# ⚙️ 11. Wichtige Begriffe kurz erklärt
| Begriff     | Bedeutung                 |
| ----------- | ------------------------- |
| SSL/TLS     | Verschlüsselungsprotokoll |
| HTTPS       | HTTP + TLS                |
| Zertifikat  | Identität der Website     |
| CA          | Zertifizierungsstelle     |
| Private Key | geheimer Schlüssel        |
| Public Key  | öffentlicher Schlüssel    |

***

# 🧠 12. Die 3 Kernpunkte zum Merken

👉\
**1. Verschlüsselung schützt Daten**

👉\
**2. Zertifikat bestätigt Identität**

👉\
**3. Vertrauen entsteht durch die Kette**

***

# 🚀 Bonus (für dich als SysAdmin)

👉 Checkliste bei Problemen:

* Zertifikat gültig?
* Domain korrekt?
* Zwischenzertifikat vorhanden?
* Uhrzeit korrekt?
* Root im Trust Store?

***

# 💥 Finaler Merksatz

👉\
**„HTTPS ist nicht nur Verschlüsselung –\
es ist Vertrauen + Identität + Absicherung.“**

***
