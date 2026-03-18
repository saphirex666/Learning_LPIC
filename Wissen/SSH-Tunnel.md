14. Sicherheitsaspekte
SSH ist stark, aber nur mit sauberer Konfiguration.
Empfehlungen
Keine Passwörter, sondern Keys nutzen
Besser:
PasswordAuthentication no
PubkeyAuthentication yes
Root-Login möglichst einschränken
Statt direkt root lieber Benutzer + sudo.
Port 22 absichern
z. B. mit:
Firewall
Fail2ban
erlaubten IPs
anderem SSH-Port als Zusatzmaßnahme
Tunnel bewusst einsetzen
Nicht versehentlich Dienste freilegen, die intern bleiben sollten.
Private Keys niemals weitergeben
Der private Schlüssel bleibt ausschließlich beim Besitzer.
15. Nützliche Optionen
Tunnel im Hintergrund starten
ssh -f -N -L 11115:127.0.0.1:11115 root@192.168.212.20
-f schickt SSH nach erfolgreicher Anmeldung in den Hintergrund
-N startet nur den Tunnel
Verbindungsprüfung
ssh -v -N -L 11115:127.0.0.1:11115 root@192.168.212.20
Mit -v siehst du Debug-Infos.
Mehr Details:
ssh -vvv ...
Keepalive für stabile Tunnel
ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=3 -N -L 11115:127.0.0.1:11115 root@192.168.212.20
Das hilft, wenn Tunnel sonst unbemerkt abbrechen.
16. Häufige Fehler
Schreibfehler bei Zieladresse
Falsch:
ssh -N -L 11115:.0.0.1:11115 root@192.168.212.20
Richtig:
ssh -N -L 11115:127.0.0.1:11115 root@192.168.212.20
Falsches Verständnis von 127.0.0.1
Bei -L ist 127.0.0.1 normalerweise die Sicht des Remote-Servers, nicht deines lokalen Systems.
Dienst hört nicht auf dem Zielport
Auch wenn der Tunnel korrekt ist, funktioniert nichts, wenn auf dem Zielserver gar nichts auf 127.0.0.1:11115 lauscht.
Prüfen auf dem Zielserver:
ss -tulpn | grep 11115
Firewall blockiert Zugriff
Sowohl lokal als auch auf dem SSH-Server können Firewalls eine Rolle spielen.
17. Typische Diagnosebefehle
Prüfen, ob SSH-Server erreichbar ist
ssh root@192.168.212.20
Prüfen, ob Dienst auf Zielport läuft
ss -tulpn | grep 11115
Prüfen, ob lokaler Port offen ist
ss -tulpn | grep 11115
Verbose SSH-Ausgabe
ssh -vvv -N -L 11115:127.0.0.1:11115 root@192.168.212.20
18. Praxisbeispiele als Vorlage
Lokaler Tunnel zu Remote-Webdienst
ssh -N -L 8080:127.0.0.1:80 root@192.168.212.20
Lokaler Tunnel zu Datenbank
ssh -N -L 3307:127.0.0.1:3306 root@192.168.212.20
Reverse Tunnel zurück auf lokalen SSH-Port
ssh -N -R 2222:127.0.0.1:22 root@server.example.com
Dateiübertragung mit SCP
scp backup.tar.gz root@192.168.212.20:/root/
Synchronisierung mit rsync
rsync -avz -e ssh /daten/ root@192.168.212.20:/backup/
19. Zusammenfassung
SSH ist weit mehr als nur Remote-Login.
Es ist ein universelles Werkzeug für:
sichere Administration
sichere Dateiübertragung
Portweiterleitungen
das Erreichen interner Dienste
Reverse-Verbindungen aus abgeschotteten Netzen
Dein Beispiel:
ssh -N -L 11115:127.0.0.1:11115 root@192.168.212.20
bedeutet ganz konkret:
lokal Port 11115 öffnen
Verbindung verschlüsselt per SSH aufbauen
den Dienst auf 192.168.212.20, der dort auf 127.0.0.1:11115 läuft, lokal verfügbar machen
keine Shell starten, nur Tunnel
20. Merksatz
-L
Mein lokaler Port geht durch SSH zu einem Ziel auf der Gegenseite
-R
Ein entfernter Port geht durch SSH zurück zu meinem lokalen Gerät
-D
SSH baut mir lokal einen SOCKS-Proxy
