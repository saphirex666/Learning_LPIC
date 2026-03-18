Ich stelle dir eine kompakte, aber vollständige Wissensseite dazu zusammen: Grundlagen, typische Tunnelarten, Key-Setup, Datenübertragung, Portmapping und dein konkretes Beispiel sauber erklärt.

Nachgedacht für 5s

# SSH-Verbindungen und SSH-Tunnel

SSH steht für **Secure Shell** und ist ein Protokoll, mit dem man sich **verschlüsselt** mit einem anderen System verbindet. Es wird genutzt, um:

* entfernte Systeme sicher zu administrieren
* Dateien sicher zu übertragen
* Ports und Dienste durch Tunnel abzusichern
* interne Dienste erreichbar zu machen, ohne sie direkt ins Netz freizugeben

SSH ist damit nicht nur ein Login-Werkzeug, sondern auch ein **sicherer Transportkanal** für Daten und Netzwerkverbindungen.

***

## 1. Grundprinzip von SSH

Eine normale SSH-Verbindung sieht so aus:
```bash
ssh benutzer@server
```
Beispiel:
```bash
ssh root@192.168.178.20
```
Dabei passiert im Hintergrund:

1. Der Client baut eine Verbindung zum SSH-Server auf, meist auf **Port 22**
2. Client und Server handeln Verschlüsselung und Schlüssel aus
3. Der Benutzer authentifiziert sich
4. Danach läuft die gesamte Sitzung verschlüsselt

SSH schützt damit vor dem Mitlesen und Manipulieren auf dem Transportweg.

***

## 2. Authentifizierung bei SSH

Es gibt zwei typische Wege:

### Passwort-Authentifizierung

Einfach, aber schwächer.\
Der Benutzer meldet sich mit Passwort an.

### Schlüsselbasierte Authentifizierung

Deutlich besser und im professionellen Umfeld Standard.\
Hier gibt es:

* **privaten Schlüssel** → bleibt auf deinem Gerät

* **öffentlichen Schlüssel** → wird auf dem Zielsystem hinterlegt

Nur wer den privaten Schlüssel besitzt, kann sich anmelden.

***

# 3. SSH-Keygenerierung

## 3.1 Neuen SSH-Key erzeugen

Empfohlen ist heute meist **ed25519**:
```bash
ssh-keygen -t ed25519 -C "mein-schluessel"
```

Danach wirst du gefragt:

* Speicherort
* optionale Passphrase

Typische Dateien:
```bash
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Bedeutung:

* `id_ed25519` → **privater Schlüssel**
* `id_ed25519.pub` → **öffentlicher Schlüssel**

***

## 3.2 Öffentlichen Schlüssel auf Zielsystem kopieren

Am einfachsten:
```bash
ssh-copy-id root@192.168.178.20
```
Alternativ manuell:

Inhalt des Public Keys anzeigen:
```bash
cat ~/.ssh/id_ed25519.pub
```
Dann auf dem Zielsystem in folgende Datei eintragen:

```bash
~/.ssh/authorized_keys
```
Wichtig auf dem Server:
```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
***

## 3.3 Verbindung mit Schlüssel aufbauen
```bash
ssh -i ~/.ssh/id_ed25519 root@192.168.178.20
```
Falls der Standardname genutzt wird, reicht oft:
```bash
ssh root@192.168.212.20
```
***

## 3.4 Warum Keys besser sind

Vorteile:

* sicherer als Passwörter
* kann automatisiert genutzt werden
* kann mit Passphrase geschützt werden
* gut für Tunnel, Skripte und Backups

***

# 4. Datenübertragung über SSH

SSH kann nicht nur Shell-Zugriffe, sondern auch Dateien sicher übertragen.

## 4.1 SCP

Einfaches Kopieren über SSH.

### Lokale Datei zum Server

```bash
scp datei.txt root@192.168.178.20:/root/
```

### Datei vom Server lokal holen
```bash
scp root@192.168.178.20:/root/datei.txt .
```
### Ganze Ordner kopieren
```bash
scp -r meinordner root@192.168.178.20:/root/
```
***

## 4.2 RSYNC über SSH

Besser für größere oder wiederholte Übertragungen:

```bash
rsync -avz -e ssh /quelle/ root@192.168.178.20:/ziel/
```

Vorteile:

* schnell
* überträgt nur Änderungen

* gut für Backups und Synchronisation

***

# 5. SSH-Tunneling – Grundidee

Mit SSH kann man **Netzwerkverbindungen durch einen verschlüsselten Tunnel leiten**.

Das ist nützlich, wenn:

* ein Dienst nur intern erreichbar ist
* ein Webinterface sicher genutzt werden soll
* ein Port nicht direkt freigegeben werden soll
* man interne Dienste über einen SSH-Server erreichen möchte

Es gibt drei zentrale Tunnelarten:

1. **Local Forwarding** (`-L`)
2. **Remote Forwarding / Reverse Tunnel** (`-R`)
3. **Dynamic Forwarding** (`-D`)

***

# 6. Local Port Forwarding (`-L`)

Dabei öffnet dein **lokaler Rechner** einen Port, der durch SSH zu einem Ziel auf der Gegenseite weitergeleitet wird.

Syntax:

```bash
ssh -L [lokaler_port]:[ziel_host]:[ziel_port] benutzer@ssh-server
```

## Beispiel

```bash
ssh -L 8080:127.0.0.1:80 root@192.168.178.20
```

Bedeutung:

* auf deinem lokalen Rechner wird Port `8080` geöffnet
* Zugriffe darauf werden durch SSH geleitet
* auf dem Server `192.168.178.20` wird dann `127.0.0.1:80` angesprochen

Das heißt:\
Wenn auf dem entfernten Server ein Webdienst **nur lokal** auf `127.0.0.1:80` hört, kannst du ihn lokal über `http://127.0.0.1:8080`erreichen.

***

## Wichtiger Denkfehler bei `-L`

Bei `-L` bezieht sich der Zielhost **aus Sicht des SSH-Servers**.

Also:

```bash
ssh -L 8080:127.0.0.1:80 root@server
```

bedeutet nicht:\
„Nimm meinen lokalen Port 8080 und leite ihn zu meinem lokalen 127.0.0.1:80“

sondern:

„Nimm meinen lokalen Port 8080 und leite ihn über den SSH-Server zu **127.0.0.1:80 auf dem Zielserver**.“

***

# 7. Remote Port Forwarding / Reverse SSH Tunnel (`-R`)

Das ist der umgekehrte Weg.

Hier wird ein Port **auf dem entfernten Server** geöffnet, und Zugriffe darauf werden zurück zu einem Dienst auf deinem lokalen Rechner geleicht.

Syntax:
```bash
ssh -R [entfernter_port]:[lokaler_host]:[lokaler_port] benutzer@ssh-server
```

## Beispiel
```bash
ssh -R 2222:127.0.0.1:22 root@server
```

Bedeutung:

* auf dem entfernten Server wird Port `2222` geöffnet
* Zugriffe auf diesen Port werden zurück zu deinem lokalen Rechner auf `127.0.0.1:22` geleitet

Das ist besonders nützlich, wenn dein lokaler Rechner:

* hinter NAT sitzt
* keine Portfreigabe hat
* aber selbst trotzdem erreichbar gemacht werden soll

***

## Praxisnutzen Reverse Tunnel

Typischer Fall:

* Ein Gerät steht hinter einer Fritzbox oder in einem fremden Netz
* Eingehende Verbindungen sind nicht möglich
* Das Gerät baut **aktiv selbst** eine SSH-Verbindung zu einem Server auf
* Über `-R` stellt es dann einen Rückkanal bereit

So kann man z. B. von außen auf ein internes Gerät zugreifen, obwohl dieses keine öffentliche Freigabe hat.

# 8. Dynamic Forwarding (`-D`)

Dabei stellt SSH lokal einen SOCKS-Proxy bereit.

```bash
ssh -D 1080 root@192.168.178.20
```

Dann kann eine Anwendung den lokalen SOCKS-Proxy auf `127.0.0.1:1080` nutzen.

Nützlich für:

* Browser-Traffic
* Testzwecke
* flexible Tunnel-Nutzung

***

# 9. Option `-N`

Die Option:

```Bash
-N
```

bedeutet:

**Keine Remote-Shell ausführen**.

SSH baut also nur den Tunnel auf, aber öffnet keine interaktive Konsole.

Das ist ideal für Tunnel.

***

# 10. Dein Beispiel erklärt

Du hast angegeben:

```bash
ssh -N -L 11115:.0.0.1:11115 root@192.168.178.20
```

Hier ist sehr wahrscheinlich ein Schreibfehler enthalten.

Gemeint ist fast sicher:

```bash
ssh -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

oder kürzer:

```bash
ssh -N -L 11115:localhost:11115 root@192.168.178.20
```

***

## Bedeutung deines Befehls

```Bash
ssh -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

heißt:

* öffne lokal Port `11115`
* leite alles durch die SSH-Verbindung
* verbinde auf dem Zielsystem `192.168.212.20` zu `127.0.0.1:11115`
* starte keine Shell, nur den Tunnel

***

## Ergebnis

Wenn auf `192.168.212.20` ein Dienst läuft, der nur auf `127.0.0.1:11115` lauscht, dann kannst du ihn auf deinem lokalen Rechner so erreichen:

```bash
127.0.0.1:11115
```

oder im Browser:

```bash
http://127.0.0.1:11115
```

***

## Wann das sinnvoll ist

Wenn ein Dienst auf dem Zielserver absichtlich **nicht direkt im Netzwerk freigegeben** ist, aber du ihn dennoch sicher nutzen willst.

Typische Beispiele:

* interne Weboberfläche
* Datenbankport
* API-Dienst
* Admin-Interface
* lokaler Ollama- oder Control-Port

# 11. Portmapping bei SSH

Mit „Portmapping“ meint man hier meist die gezielte Weiterleitung eines Ports von einer Seite zur anderen.

## Arten des Mappings

### Lokales Mapping

Lokaler Port → entfernter Host/Port

```bash
ssh -L 8080:127.0.0.1:80 user@server
```

### Entferntes Mapping

Entfernter Port → lokaler Host/Port

```bash
ssh -R 9000:127.0.0.1:9000 user@server
```

### SOCKS / dynamisch

Lokaler Port → variable Ziele über Proxy

```bash
ssh -D 1080 user@server
```

***

# 12. Beispiele aus der Praxis

## 12.1 Webinterface auf Remote-Server sicher nutzen

```bash

ssh -N -L 8443:127.0.0.1:443 root@192.168.178.20
```

Dann lokal:

```Bash
https://127.0.0.1:8443
```

***

## 12.2 Datenbankport tunneln
```bash
ssh -N -L 3307:127.0.0.1:3306 root@192.168.178.20
```

Dann lokal mit einem Tool auf `127.0.0.1:3307` verbinden.

***

## 12.3 Internen Dienst eines anderen Hosts hinter dem SSH-Server erreichen

```bash
ssh -N -L 8080:192.168.1.50:80 root@192.168.178.20
```

Bedeutung:

* lokal Port `8080`
* über SSH-Server `192.168.178.20`
* Ziel ist aber ein anderer Host aus Sicht des SSH-Servers: `192.168.1.50:80`

Das ist sehr mächtig, weil der SSH-Server als Sprungpunkt dienen kann.

***

# 13. Reverse SSH Tunnel in der Praxis

## Beispiel

Ein Gerät hinter NAT soll von außen erreichbar sein:

```bash
ssh -N -R 2222:127.0.0.1:22 root@oeffentlicher-server
```

Dann kann man auf dem öffentlichen Server später z. B. zugreifen auf:

```bash
ssh -p 2222 localhost
```

Dadurch landet man auf dem internen Gerät, das den Reverse Tunnel aufgebaut hat.

***

## Typische Einsatzfälle

* Raspberry Pi in Fremdnetz erreichbar machen
* Home Assistant oder Webdienst temporär zugänglich machen
* Wartung interner Systeme
* Rückkanal für Geräte ohne Portfreigabe

***

# 14. Sicherheitsaspekte

SSH ist stark, aber nur mit sauberer Konfiguration.

## Empfehlungen

### Keine Passwörter, sondern Keys nutzen

Besser:

```bash
PasswordAuthentication no
PubkeyAuthentication yes
```

### Root-Login möglichst einschränken

Statt direkt `root` lieber Benutzer + `sudo`.

### Port 22 absichern

z. B. mit:

* Firewall
* Fail2ban
* erlaubten IPs
* anderem SSH-Port als Zusatzmaßnahme

### Tunnel bewusst einsetzen

Nicht versehentlich Dienste freilegen, die intern bleiben sollten.

### Private Keys niemals weitergeben

Der private Schlüssel bleibt ausschließlich beim Besitzer.

***

# 15. Nützliche Optionen

## Tunnel im Hintergrund starten

```bash
ssh -f -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

* `-f` schickt SSH nach erfolgreicher Anmeldung in den Hintergrund
* `-N` startet nur den Tunnel

***

## Verbindungsprüfung

```bash
ssh -v -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

Mit `-v` siehst du Debug-Infos.

Mehr Details:

```bash
ssh -vvv ...
```

***

## Keepalive für stabile Tunnel

```bash
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=3 -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

Das hilft, wenn Tunnel sonst unbemerkt abbrechen.

***

# 16. Häufige Fehler

## Schreibfehler bei Zieladresse

Falsch:
```bash
ssh -N -L 11115:.0.0.1:11115 root@192.168.178.20
```
Richtig:
```bash
ssh -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

***

## Falsches Verständnis von `127.0.0.1`

Bei `-L` ist `127.0.0.1` normalerweise die Sicht **des Remote-Servers**, nicht deines lokalen Systems.

***

## Dienst hört nicht auf dem Zielport

Auch wenn der Tunnel korrekt ist, funktioniert nichts, wenn auf dem Zielserver gar nichts auf `127.0.0.1:11115` lauscht.

Prüfen auf dem Zielserver:

```
ss -tulpn | grep 11115
```

***

## Firewall blockiert Zugriff

Sowohl lokal als auch auf dem SSH-Server können Firewalls eine Rolle spielen.

***

# 17. Typische Diagnosebefehle

## Prüfen, ob SSH-Server erreichbar ist

```bash
ssh root@192.168.212.20
```

## Prüfen, ob Dienst auf Zielport läuft

```bash
ss -tulpn | grep 11115
```

## Prüfen, ob lokaler Port offen ist

```bash
ss -tulpn | grep 11115
```

## Verbose SSH-Ausgabe

```Bash
ssh -vvv -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

***

# 18. Praxisbeispiele als Vorlage

## Lokaler Tunnel zu Remote-Webdienst

```Bash
ssh -N -L 8080:127.0.0.1:80 root@192.168.178.20
```

## Lokaler Tunnel zu Datenbank

```Bash
ssh -N -L 3307:127.0.0.1:3306 root@192.168.178.20
```

## Reverse Tunnel zurück auf lokalen SSH-Port

```bash
ssh -N -R 2222:127.0.0.1:22 root@server.example.com
```

## Dateiübertragung mit SCP

```bash
scp backup.tar.gz root@192.168.178.20:/root/
```

## Synchronisierung mit rsync

```bash
rsync -avz -e ssh /daten/ root@192.168.178.20:/backup/
```

***

# 19. Zusammenfassung

SSH ist weit mehr als nur Remote-Login.

Es ist ein universelles Werkzeug für:

* sichere Administration
* sichere Dateiübertragung
* Portweiterleitungen
* das Erreichen interner Dienste
* Reverse-Verbindungen aus abgeschotteten Netzen

Dein Beispiel:

```Bash
ssh -N -L 11115:127.0.0.1:11115 root@192.168.178.20
```

bedeutet ganz konkret:

* lokal Port `11115` öffnen
* Verbindung verschlüsselt per SSH aufbauen
* den Dienst auf `192.168.178.20`, der dort auf `127.0.0.1:11115` läuft, lokal verfügbar machen
* keine Shell starten, nur Tunnel

***

# 20. Merksatz

## `-L`

**Mein lokaler Port geht durch SSH zu einem Ziel auf der Gegenseite**

## `-R`

**Ein entfernter Port geht durch SSH zurück zu meinem lokalen Gerät**

## `-D`

**SSH baut mir lokal einen SOCKS-Proxy**

***
