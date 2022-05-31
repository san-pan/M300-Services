# Einleitung allgemein 
300 Plattformübergreifende Dienste in ein Netzwerk integrieren
- Kompetenz:
    Plattformübergreifende Dienste nach Vorgabe für eine heterogene Systemumgebung konfigurieren, in Betrieb nehmen, testen und freigeben

# Inhaltsverszeichnis

## Virtualisierung
### 01-Container
#### Was ist ein Container?
Container ändern die Art und Weise, wie wir Software entwickeln, verteilen und laufen lassen, grundlegend.

Entwickler können Software lokal bauen, die woanders genauso laufen wird – sei es ein Rack in der IT-Abteilung, der Laptop eines Anwenders oder ein Cluster in der Cloud.

Administratoren können sich auf die Netzwerke, Ressourcen und die Uptime konzentrieren und müssen weniger Zeit mit dem Konfigurieren von Umgebungen und dem Kampf mit Systemabhängigkeiten verbringen.

Merkmale:

- Container teilen sich Ressourcen mit dem Host-Betriebssystem
- Container können im Bruchteil einer Sekunde gestartet und gestoppt werden
- Anwendungen, die in Containern laufen, verursachen wenig bis gar keinen Overhead
- Container sind portierbar --> Fertig mit "Aber bei mir auf dem Rechner lief es doch!"
- Container sind leichtgewichtig, d.h. es können dutzende parallel betrieben werden.
- Container sind "Cloud-ready"!

### 02-Docker
#### Was ist Docker?
Docker ist eine Freie Software zur Isolierung von Anwendungen mit Hilfe von Containervirtualisierung. Es vereinfacht die Bereitstellung von Anwendungen, weil sich Container, die alle nötigen Pakete enthalten, leicht als Dateien transportieren und installieren lassen.
#### Auftrag
Installation des Images:
```
docker pull mysql
```
Um zu sehen, welche images auf dem Server sind:
```
docker image ls
```
oder
```
docker images
```
Befehl um den Container zu erstellen mit dem folgenden Image, welches einen Namen und ein Passwort erhält. 3306:3306 ist die Portweiterleitung von dem VM zum Container. Das Linke ist der Port von der VM und das Rechte das vom Contaiener. Mysql:latest ist zum definieren, damit das Image auf dem neusten Stand ist. (Normalerweise ist dies schon die neuste Version)
```
docker run --name mysql -v /my/custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=Password123 -p 3306:3306 -d mysql:latest
```
Um die Container-ID zu erhalten
```
docker ps
```
Container stoppen
```
docker stop [Container-ID]
```
Zeigt den gestoppten Container noch an, weil er noch läuft und nicht richtig gestoppt wurde.
```
docker ps -a
```
Um den Container vollständig zu entfernen
```
docker rm d99168928220
```
Um den Container wieder zu erstellen kann man den "docker run..." Befehl wieder ausführen und der Container ist wieder erstellt werden.

Um alle vorhandenen IP-Adressen herauszufinden
```
ip a/ifconfig
```

```
ip a
```
Nachdem der Container läuft und funktioniert, kann man nun ein mysql-Client erstellen, mit dem man sich auf den Server verbinden kann.
[Installationslink](https://www.dbvis.com/database/mysql/?gclid=Cj0KCQjw-daUBhCIARIsALbkjSbFZM1ORZVbrphTXrHz7n-8fBfk0KyUHg75zNLHglJFml5YKFVm0QsaAq0mEALw_wcB)

Nach der Installation:

Connections/Create a new connection/MySQL8 --> Hier die nötigen Logindaten eintragen

Wenn die Verbindung erfolgreich war, kann man nun zurück auf den Server und dort sich auf den Container verbinden und so auf die Datenbank zugreifen.
```
docker exec -it [Container-ID] /bin/bash
```
```
cd /var/lib/mysql
```
Zum Verlassen des Containers
```
exit
```
## Reflexion
Lernprozess festgehalten (Form frei wählbar)


- - -
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/ch/"><img alt="Creative Commons Lizenzvertrag" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/3.0/ch/88x31.png" /></a><br />Dieses Werk ist lizenziert unter einer <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/ch/">Creative Commons Namensnennung - Nicht-kommerziell - Weitergabe unter gleichen Bedingungen 3.0 Schweiz Lizenz</a>

- - -
