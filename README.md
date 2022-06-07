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
#### Auftrag - Docker Images und Run

[Docker Cheat Sheet](https://tbzedu.sharepoint.com/sites/campus/students/it/Forms/AllItems.aspx?id=%2Fsites%2Fcampus%2Fstudents%2Fit%2F%5Fread%2Donly%2FM300%2F2%2D%20Unterlagen%2FDocker%5Fcheat%2Dsheet%2Dv2%2Epdf&parent=%2Fsites%2Fcampus%2Fstudents%2Fit%2F%5Fread%2Donly%2FM300%2F2%2D%20Unterlagen&p=true&ga=1)

Es wird ein Container mit einer laufenden Datenbank erstellt, welche durch eine Portweiterleitung von 3306 es möglich macht, vom Host auf den Client zuzugreifen.


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
docker rm [Container ID]
```
Um den Container wieder zu erstellen kann man den "docker run..." Befehl wieder ausführen und der Container ist wieder erstellt werden.

Um alle vorhandenen IP-Adressen herauszufinden
```
ip a/ifconfig
```

```
ip a
```
Nachdem der Container läuft und funktioniert, kann man nun ein mysql-Client installieren, mit dem man sich auf den Server verbinden kann:
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

#### Auftrag - Docker Volumes
Es konnte nun eine Datenbank vom Host mit dem Container verbunden werden, jedoch wenn man den Container löscht, geht diese ebenfalls verloren. Damit dies nicht passiert wird ein Volume im Container erstellt, welches alle Daten abspeichern kann. Dafür wird ein Volume auf der VM erstellt mit einem einzigartigem Namen und dies wir gemountet mit dem Verzeichnis /var/lib/mysql, welches auf dem Container abgelegt ist.

Dafür wird zuerst der Container gelöscht, das Volume erstellt und der Container wieder aufgesetzt.
```
docker stop [Container ID]
```
```
docker rm [Container ID]
```
Um alle volume-Befehle zu sehen:
```
docker volume
```
Alle Volumes, welche nicht gebraucht werden, können gelöscht werden:
```
docker volume prune
```
Um alle Volumes zu sehen:
```
docker volume ls
```
Volume erstellen:
```
docker volume create mydbstore
```
Pfad zum lokalen Speicherort sehen:
```
docker volume inspect mydbstore
```
Damit die Daten nun auf der VM abgespeichert werden und nicht mehr im Container wird der folgende run Befehl ergänzt. Der Teil "-v mydbstore:/var/lib/mysql" befiehlt dem Volume, dass es zum "var/lib/mysql" zeigen soll. Alles was dann dort abgespeichert wird, ist nicht mehr im Container sondern im Volume von der VM.
```
docker run --name mysql -v /my/custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=Password123 -v mydbstore:/var/lib/mysql -p 3306:3306 -d mysql:latest
```
Es wird nun nochmals eine Datenbank erstellt, der Container gelöscht und und wieder erstellt. Die Datenbank ist wieder vorhanden, wie es vorher war.
```
docker stop [Container ID]
```
```
docker rm [Container ID]
```
```
docker run --name mysql -v /my/custom:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=Password123 -v mydbstore:/var/lib/mysql -p 3306:3306 -d mysql:latest
```
```
docker exec -it [Container ID] /bin/bash
```
```
ls -l /var/lib/mysql
```

#### Docker build
Es werden Docker-Images erstellt. Dabei gibt es zwei Varianten:
1. wie man einen laufenden Container mit Modifikationen ein neues Image kann bauen, welches basierend auf dem laufenden Container ist.
2. Ähnlich wie das Vagrant-File. Es wird ein Docker-File per Code definiert, was alles in dem Image vorhanden sein soll.

**Erste Variante**
Neuen Container erstellen:
```
docker run -it --name cowsay --hostname cowsay ubuntu bash
```
```
exit
```
Auf der VM suchen, ob das Image vorhanden ist
```
docker image ls
```
Container starten
```
docker start cowsay
```
Auf den Container zugreifen und Updates durchführen. Danach das Programm "fortune" herunterladen
```
docker exec -it cowsay bash
```
```
apt-get update
```
```
apt-get install -y cowsay fortune
```
Testen, ob die Installation erfolgreich war
```
/usr/games/fortune oder /usr/games/fortune |/usr/games/cowsay
```
Container mit dem folgenden Image erstellen:
```
docker commit cowsay img/cowsay
```
Kontrollen, ob es erstellt wurde
```
docker history /img/cowsay
```
Neuen Container mit dem Image vom "Cowsay" Container erstellen
```
docker run -it --name newcowsay --hostname newcowsay img/cowsay
```
Wenn man nun den folgenden Befehl eingibt sieht man, dass das Image auf dem "Newcowsay" läuft
```
/usr/games/fortune |/usr/games/cowsay
```

**Zweite Variante**

```
docker image ls
```

```
docker image ls
```

```
docker image ls
```

```
docker image ls
```

```
docker image ls
```

## Reflexion
Lernprozess festgehalten (Form frei wählbar)


- - -
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/ch/"><img alt="Creative Commons Lizenzvertrag" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/3.0/ch/88x31.png" /></a><br />Dieses Werk ist lizenziert unter einer <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/3.0/ch/">Creative Commons Namensnennung - Nicht-kommerziell - Weitergabe unter gleichen Bedingungen 3.0 Schweiz Lizenz</a>

- - -
