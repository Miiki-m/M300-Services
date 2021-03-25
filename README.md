# M300-Services

###### Mischo Micic

## Einleitung

Das ist die Dokumentation für die Leistungsbeurteilung im Modul 300, hier wird der Fortschritt, Neues Wissen und weiteres festgehalten.


## Inhalt
- [Lernumgebung](#Lernumgebung)
- [Eigene VM](#Eigene-VM)
- [Persönlicher Wissensstand](#Persönlicher-Wissensstand)
- [Vagrant](#Vagrant)
- [Die VMs](#Die-VMs)
- [Wissenszuwachs V2](#Wissenszuwachs-V2)
- [Reflexion](#Reflexion)

---

## Lernumgebung
Folgende schriitte sind Nötig für eine Voll-Funktionsfähige Lernumgebung.

> **TODO** VirtualBox einrichten

Die VirtualBox software kann unter https://www.virtualbox.org/wiki/Downloads
Heruntergeladen werden.
> **TODO** Vagrant einrichten

Die Vagrant software kann unter https://www.vagrantup.com/downloads
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

## Eigene VM

Meine eigene VM habe ich vorgefertigt aus dem Vagrant Boxes Share genommen https://app.vagrantup.com/ubuntu/boxes/xenial64 hierbei handelt es sich um eine ganz normale Linux
VM.

---

## Persönlicher Wissensstand

Ganz am anfang habe ich Vagrant und die weiteren Programme nicht gekannt, jedoch habe ich jetzt ein bisschen erfahrungen gesammelt doch selbst würde ich sagen dass da noch einiges fehlt bis ich Sattelfest bin.

---
## Vagrant
Vagrant ist ein Werkzeug zum Erstellen und Verwalten von Umgebungen mit virtuellen Maschinen in einem einzigen Arbeitsablauf. Mit einem einfach zu bedienenden Workflow und dem Fokus auf Automatisierung senkt Vagrant die Einrichtungszeit für Entwicklungsumgebungen & erhöht die Produktivität.

#### Vagrant Commands
```
Vagrant box add [box add]: downloaded Vagrant image local store
Vagrant box remove [box add]: entfernt Vagrant image local store
Vagrant box list: Zeigt downloaded boxes an
vagrant init [box]: initialisiert Vagrantfile
Vagrant up: startet VM's aus Vagrantfile
Vagrant status: Zeigt Status der Vagrant box
Vagrant halt: stopt VM's
Vagrant destroy: zerstört VM's
Vagrant ssh [vmname]: Verbindet per ssh auf VM
vagrant version: zeig Version von Vagrant
vagrant port: Zeigt portmapping zwischen host und gast
```
---
# Die VMs

## Firewall
Eine Firewall ist ein Sicherheitsgerät - Computer-Hardware oder -Software -, das zum Schutz des Netzwerks beitragen kann, indem es den Datenverkehr filtert und Aussenstehende daran hindert, unbefugten Zugriff auf die privaten Daten auf dem Computer zu erlangen

#### Security
Für die Sicherheit habe ich die Entsprechenden Ports geöffnet die Nötig sind und Regeln minimal auf das nötigste angepasst, ausserdem wurde ein Reverse-Proxy eingerichtet um beizustehen, dieser läuft auf einer zweiten VM. Die Benutzer wurden angepasst und ein Test User mitsamt Test Gruppe und Test File wurden angelegt.

#### Vagrant File FW

```
Vagrant.configure("2") do |config|
config.vm.box = "debian/buster64"
config.vm.provider "virtualbox" do |vb|
end
config.vm.provision "shell", inline: <<-SHELL
sudo apt-get install ufw
sudo ufw --force enable
sudo ufw allow 80/tcp
sudo ufw allow from any to any port 22
sudo ufw allow from any to any port 3306
SHELL
end
```
---

## Reverse Proxy
In Computernetzwerken wie dem Internet ist ein Reverse-Proxy ein gängiger Typ von Proxy-Server, der vom öffentlichen Netzwerk aus zugänglich ist. Grosse Websites und Content-Delivery-Netzwerke verwenden Reverse-Proxys - zusammen mit anderen Techniken -, um die Last zwischen internen Servern auszugleichen. Reverse-Proxys können einen Cache mit statischen Inhalten vorhalten, was die Last auf diesen internen Servern und dem internen Netzwerk weiter reduziert.

#### Vagrant File R-Proxy
```
Vagrant.configure("2") do |config|
config.vm.box = "debian/buster64"
config.vm.provider "virtualbox" do |vb|
end
config.vm.provision "shell", inline: <<-SHELL
sudo apt-get install apache2 -y
sudo apt-get install libapache2-mod-proxy-html -y
sudo apt-get install libxml2-dev -y
sudo a2enmod proxy
sudo a2enmod proxy_html
sudo a2enmod proxy_http
sudo systemctl restart apache2
sudo echo "ServerName localhost" >> /etc/apache2/apache2.conf
sudo systemctl restart apache2
sudo touch /etc/apache2/sites-enabled/001-reverseproxy.conf
sudo echo "ProxyRequests Off
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>
    ProxyPass /master http://master
    ProxyPassReverse /master http://master" >> /etc/apache2/sites-enabled/001-reverseproxy.conf
SHELL
end
```
---
## Import Box
Die von https://app.vagrantup.com/generic/boxes/devuan2 importierte Box wird mit den oben genannten befehlen hier Importiert. Ich werde kein Vagrant File aufzeigen da keine speziellen Änderungen gemacht wurden an der Import Box

#### Inbetriebnahme der Import Box
```
vagrant init boxes/devuan2
vagrant up
vagrant SSH
```
Wie man sieht ist das ganze ziemlich einfach gestaltet.

---

## Wissenszuwachs V2

Die Unsicherheiten die ich am Anfang hatte bezüglich der Umgebung und der Vagrant Software haben sich nach den ersten VMs schnell verflüchtigt, ich habe dazu gelernt wie man schnell und einfach VMs erstellen kann um software und konfigurationen zu testen, Firewalls unkompliziert einrichten und weitere tätigkeiten die im zusammenhang mit den Aufgaben die uns gegeben wurde stehen.

---

## Reflexion

Der Start war wie vorhin angemerkt etwas holprig da ich mich bis dato nicht all zu sehr mit Vagrant oder derartigen Software-Typen befasst habe, nach den ersten erfahrungen habe ich schnell gemerkt das man dass ganze doch sehr schnell anlernen und vertiefen kannm Mittlerweile fällt es mir nicht mehr schwer aufgaben im Vagrant zu bewältigen.
