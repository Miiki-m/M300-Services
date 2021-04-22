# M300-Services

###### Mischo Micic

## Einleitung

Das ist die Dokumentation für die Leistungsbeurteilung 2 im Modul 300, hier wird der Fortschritt, Neues Wissen und weiteres festgehalten.


## Inhalt
- [Lernumgebung](#Lernumgebung)
- [Persönlicher Wissensstand](#Persönlicher-Wissensstand)
- [Docker](#Docker)
- [Die Images und Container](#Die-Images-und-Container)
- [Wissenszuwachs V2](#Wissenszuwachs-V2)
- [Reflexion](#Reflexion)

---

## Lernumgebung
Folgende schriitte sind Nötig für eine Voll-Funktionsfähige Lernumgebung.

> **TODO** VirtualBox einrichten

Die VirtualBox software kann unter https://www.virtualbox.org/wiki/Downloads
Heruntergeladen werden.
> **TODO** DockerHub einrichten

Die Docker software kann unter https://docs.docker.com/docker-for-windows/install/
Heruntergeladen werden. In betrieb genommen wird diese im CMD durch die später gennanten Befehle.

> **TODO** Visual Studio Code installieren

Die VisualStudio software kann unter https://code.visualstudio.com/Download
Heruntergeladen werden.

> **TODO** Git-Client installieren

Der GitClient kann unter https://desktop.github.com/den Heruntergeladen werden. Zur vereinfachung habe ich die Desktop Version genommen da diese ein GUI besitzt.

> **TODO** SSH-Key für Client erstellen / Hinzufügen

1. Powershell Öffnen

2. Befehl Eingeben mit der Mail die für den GitHub Account verwendet wurde.
```
ssh-keygen -t rsa -b 4096 -C "test@mail.com"
```
3. Key in das entsprechende Directory abspeichern.

4. Auf https://github.com/settings/keys gehen und den Key dort hinterlegen.


---



## Persönlicher Wissensstand

Wie schon bei dem Vagrant teil aus der LB1 habe ich noch keine grossen Kentnisse und werde daher vieles dazulernen.

---
## Docker
Docker ist ein Werkzeug das Images und Container erstellen kann, die Virutaliserung ist daher ein stück einfacher, da die Containern eine abgespeckte Version eines vollen Betriebssystems darstellen und diese nicht allzu resourcen-aufwändig sind.

#### Docker Commands

| Befehl       | Beschreibung                                       |
| ------------ | -------------------------------------------------- |
| docker run   | Führt ein Befehl in einem neuen Container aus      |
| docker start | Startet einen oder mehrere gestoppte Container     |
| docker stop  | Stoppt einen oder mehrere laufende ontainer        |
| docker build | Erstellt ein Image aus einem Docker-File           |
| docker pull  | Ladet ein Image aus der Registry                   |
| docker push  | Ladet ein Image in die Registry hoch               |
| docker exec  | Führ einen Befehl in einem laufenden Container aus |
| docker volume create (NAME)| Erstellt Volumes|
| docker run -d -v (VOLUME):/world busybox ls /data |Mountet Laufwerk für einen Container|


---

### Docker Volumes

Falls ein Verzeichnis innerhalb des Containers persistent gespeichert werden soll, muss dies speziell gekennzeichnet werden. Dies kann zum Beispiel wichtig sein, wenn man eine MySQL-Datenbank innerhalb eines Containers einsetzt.




### Security
Für die Sicherheit der Umgebung habe ich ein Monitoringtool (cAdivisor) installiert das die gewünschten Container überwachen kann und allfällige störungen mittels Prometheus an den Admin melden kann.

Zusätzlich habe ich einige Best practices erstellt:

##### Read Only
Ein wichtiges file muss geöffnet jedoch nicht bearbeitet werden

```
docker run --read-only -d -t --name [Containername] [Image]
```
Dies verhindert ungewollte bearbeitungen und die Datei kann nur mit read rechten geöffnet werden.

##### User im Dockerfile

Damit der SUDO User nicht verwendet wird kann im Dockerfile folgendes hinzugefügt werden um dies abzusichern.
```
RUN groupadd -r user_grp && useradd -r -g user_grp user
USER user
```
---


#### Dockerfile Apache2

Das Dockerfile dient wie bei dem Vagrant Programm als Startpunkt für Images, in den Files kann die OS Version bei der FROM zeile definiert werden, der Author/Zuständige bei der MAINTAINER Zeile und unter den RUN Zeilen können die Befehle in der Shell des OS vorgegeben werden, dann können noch die Spezifieschen Configs mit ENV vorgegeben werden und mit EXPOSE der Port auf dem der Container HÖRT, mann kann nicht Ports für Docker selbst Öffnen lediglich für den Container als HÖREN einstellen. Dann kann man noch das Volume angeben in dem der Container sich befinden soll mit VOLUME.

```
#Apache

FROM ubuntu:14.04
MAINTAINER Maybe Miki

RUN apt-get update
RUN apt-get -q -y install apache2

# Apache Configs
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

RUN mkdir -p /var/lock/apache2 /var/run/apache2

EXPOSE 591

VOLUME /var/www/html

CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
```
---
## Import DockerHub
Die von https://hub.docker.com/search?q=&type=image importierte Box wird mit den oben genannten befehlen hier Importiert.

#### Inbetriebnahme der Import Box
```
docker pull miiki/ubuntum300:test
docker run miiki/ubuntum300:test
```
Wie man sieht ist das ganze ziemlich einfach gestaltet.


---
### Wissenszuwachs
Dazu Gelernt habe ich:
- Funktion DockerFile, Image, Container
- Funktion DockerClient & Hub
- Schreiben eines DockerFiles
- Aufbau einer Umgebung in Docker
- Securty Aspekte einer Container-Umgebung
---
## Reflexion

Mit meinem vorwissen aus der LB1 konnte ich den Start in das Thema ein bisschen besser Meistern jedoch habe ich ein bisschen gebraucht bis ich das ganze Image/Container Thema verstanden habe. Ich bin meiner Meinung nach gut vorran gekommen, jedoch habe ich mich während den Arbeiten von kleineren Problem zu sehr beeinträchtigen lassen (Prometheus).
