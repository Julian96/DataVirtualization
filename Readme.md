# Dokumentation Teiid Daten Virtualisierung
## Inhaltsverzeichnis
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Dokumentation Teiid Daten Virtualisierung](#dokumentation-teiid-daten-virtualisierung)
	- [Inhaltsverzeichnis](#inhaltsverzeichnis)
	- [Einleitung](#einleitung)
		- [Verwendete Technologien](#verwendete-technologien)
		- [Grundlagen](#grundlagen)
		- [Kompatibilität](#kompatibilität)
	- [Installation](#installation)
		- [Verbinden per Putty](#verbinden-per-putty)
		- [Verbinden per FileZilla](#verbinden-per-filezilla)
		- [Installieren von Java und Maven](#installieren-von-java-und-maven)
			- [Java](#java)
			- [Maven](#maven)
		- [Wildfly mit Teiid](#wildfly-mit-teiid)
			- [Freigabe von Ports](#freigabe-von-ports)
			- [Erklärung der Ordnerstruktur](#erklärung-der-ordnerstruktur)
				- [bin](#bin)
				- [modules](#modules)
				- [standalone](#standalone)
		- [JBoss Developer Studio mit Teiid Designer](#jboss-developer-studio-mit-teiid-designer)
	- [Benutzung des Applikationsservers](#benutzung-des-applikationsservers)
		- [Verbinden von JBoss Developer Studio mit dem Wildfly-Server](#verbinden-von-jboss-developer-studio-mit-dem-wildfly-server)
		- [User anlegen](#user-anlegen)
		- [Starten des Servers](#starten-des-servers)
			- [Starten über das Developer Studio](#starten-über-das-developer-studio)
			- [Starten über die Shell](#starten-über-die-shell)
				- [Im Vordergrund](#im-vordergrund)
				- [Im Hintergrund](#im-hintergrund)
			- [Beenden über die Shell](#beenden-über-die-shell)
				- [Im Vordergrund](#im-vordergrund)
				- [Im Hintergrund](#im-hintergrund)
		- [Öffnen der Web Management Console](#öffnen-der-web-management-console)
	- [Verbinden mit einer Datenquelle](#verbinden-mit-einer-datenquelle)
		- [Oracle](#oracle)
		- [MongoDB](#mongodb)
		- [Hive](#hive)
		- [Excel](#excel)
		- [XML](#xml)

<!-- /TOC -->
## Einleitung
In dieser Dokumentation wird die Installation der Teiid Daten Virtualisierung beschrieben, sowie das Aufsetzen des Servers und das Verbinden mit diversen Datenquellen. Zudem werden generelle Grundlagen der Verwendung des Wildfly Servers und Verwendung der JBoss Developer Studio Oberfläche geklärt. Der Ordner mit benötigten Dateien ist [hier](https://infocloud.infomotion.de/index.php/apps/files/?dir=/Teiid%20Dokumentation&fileid=4750825) zu finden.
### Verwendete Technologien
- **Rechner 1(Server)**
	- OS: [Red Hat Enterprise Linux](https://www.redhat.com/de/technologies/linux-platforms/enterprise-linux)
	- Applikationsserver: [Wildfly](http://www.wildfly.org/) (Version 11)
	- Datenvirtualisierung: [Teiid](http://teiid.io/teiid_runtimes/teiid_wildfly/downloads/) (Version 11.0.1)
	- [Java 1.8](https://java.com/de/download/)
- **Rechner 2(User)**
	- [Red Hat JBoss Developer Studio](https://developers.redhat.com/products/devstudio/overview/) mit [Teiid Designer](http://teiid.io/tools/teiid_designer/) (Version 11.1.2)
	- [Putty](https://www.putty.org/)
	- [FileZilla](https://filezilla-project.org/)

### Grundlagen
Dieses Kapitel beschäftigt sich mit der verwendeten Begriffsterminologie und dem groben Aufbau der Infrastruktur. Für die Datenvirtualisierung wird ein zwei geteiltes System verwendet. Auf der einen Seite befindet sich ein Server (VM oder ein physischer Rechner) und auf der anderen Seite der User mit seiner lokalen Maschine. In diesem Beispiel läuft eine VM auf einem anderen Rechner und wir werden mit [Putty](https://www.putty.org/) auf den Server zugreifen. Auf dem Server läuft das Betriebssystem [Red Hat Enterprise Linux](https://www.redhat.com/de/technologies/linux-platforms/enterprise-linux). Hierfür wird ein Account bei Red Hat benötigt. Auf diser Maschine wird ein Applikationsserver für die Datenvirtualisierung installiert. Hier gibt es zwei Möglichkeiten:

- [JBoss Enterprise Application Plattform](https://developers.redhat.com/products/eap/overview/)  (JBoss EAP)
- [Wildfly](http://www.wildfly.org/)  

Bei EAP handelt es sich um die offizielle Version von Red Hat und bei Wildfly um die Community Version. EAP basiert auf den Komponenten von Wildfly.  
Zusätzlich muss eine Datenvirtualisierung auf diesem Applikationsserver installiert werden. Auch hierbei gibt es wieder zwei Möglichkeiten:

- [JBoss Data Virtualization](https://developers.redhat.com/products/datavirt/overview/)
- [Teiid](http://teiid.io/)

JBoss Data Virtualization die angebotene Version von Red Hat und Teiid die Community Version. JBoss Data Virtualization wird von Red Hat in Zukunft nicht weiter entwickelt und der Support wird eingestellt (Stand: 07.08.2018).
Aus diesem Grund wird im Laufe der Dokumentation ein Wildfly Server mit installiertem Teiid verwendet.

![BildÜbersicht](./Bilder/Übersichts.png)
Um eine einfachere Konfiguration der Datenvirtualisierung zu erhalten, wird der [Teiid Designer](http://teiid.io/tools/teiid_designer/) benötigt. Diese Installation ist optional wird jedoch empfohlen. Wir verwenden den Teiid Designer als Plugin für das [Red Hat Developer Studio](https://developers.redhat.com/products/devstudio/overview/).
### Kompatibilität
Ein bestehendes Problem sind die vielen zu Verfügung stehenden Versionen von Teiid und Wildfly, sowie des Teiid Designers. Die neuste Version von Teiid ist 11.0.1 (Stand 07.08.2018) und ist mit Wildfly 11 kompatibel. Die neuste Version von Wildfly ist Wildfly 13, jedoch gibt das hierfür keine Teiid Version. Zudem ist der Teiid Designer 11.1.2 nur vollständig kompatibel und getestet mit Teiid Version 9, was ebenfalls Wildfly 9 benötigt.

| Wildfly | Teiid  | Teiid Designer | Java |
|---------|--------|----------------|------|
| 13      | -      | -              | 1.8  |
| 11      | 11.0.1 | -              | 1.8  |
| 9       | 9.3.7  | 11.1.2         | 1.7  |

Also ergeben sich zwei Möglichkeiten. Entweder es wird Wildfly 9 mit Teiid 9.3.7 und dem Teiid Designer 11.1.2 verwendet. Dies ist eine etwas veraltete Variante, jedoch funktioniert der Teiid Designer vollständig. Oder es wird Wildfly 11 mit Teiid 11.0.1 und ebenfalls dem Teiid Designer 11.1.2, jedoch können hier Probleme mit der Oberfläche im Teiid Designer entstehen und es muss für bestimmte Tasks ggfs. auf die Command-Line gewechselt werden. Die Java Version ist bedingt durch die verwendeten Komponenten und wird bei der späteren Installation eine Rolle spielen.

***Im Folgenden wird Wildfly 11 mit Teiid 11.0.1 und der Teiid Designer 11.1.2 verwendet***

## Installation
Dieses Kapitel beschäftigt sich mit der Installation der genannten Komponenten sowie deren Konfiguration zur weiteren Benutzung. Es wird davon ausgegangen, dass bereits ein Server mit dem Betriebssystem [Red Hat Enterprise Linux](https://www.redhat.com/de/technologies/linux-platforms/enterprise-linux) vorliegt. Diese Maschine sollte per SSH zu erreichen sein.
### Verbinden per Putty
Um auf das Filesystem und die Shell des Servers zuzugreifen benötigen wir [Putty](https://www.putty.org/). Installieren Sie dies auf ihrer lokalen Maschine und starten Sie Putty.

![PuttyStart](./Bilder/PuttyStartscreen.png)

Stellen Sie den ***Connection type*** auf ***SSH*** um und tragen Sie in das Feld ***Host Name*** die IP-Adresse des Servers ein sowie den ***Port*** (Standardmäßig Port: 22). Klicken Sie anschließend auf ***Load***.
Nun öffnet sich die Shell des Servers.

![ShellStart](./Bilder/ShellStartscreen.png)

Melden Sie sich mit den Benutzerdaten an. Hier ggfs. den Systemadministrator fragen.
### Verbinden per FileZilla
[FileZilla](https://filezilla-project.org/) wird für den Anfang benötigt, um den Ordner mit dem Applikationsserver auf den Server zu verschieben. Hierfür installieren Sie [FileZilla](https://filezilla-project.org/) und starten Sie die Anwendung.

Öffnen Sie über Datei -> Servermanager die Serververwaltung legen Sie einen neuen Server an.

![FileZillaServermanager](./Bilder/FileZillaServermanager.png)

Stellen Sie das ***Protokoll*** auf SFTP um und tragen Sie die IP-Adresse in das Feld ***Server***. Ebenfalls tragen Sie ihren Benutzer und das Passwort in die vorgesehenen Felder ein und klicken Sie auf verbinden. Nun haben Sie Zugriff auf das Filesystem des Servers.

### Installieren von Java und Maven
Java und Maven werden als Installation auf dem Server benötigt, damit Wildlfy und Teiid funktionieren.
#### Java
Dafür verbinden Sie sich per Putty mit dem Server und melden Sie sich an. Sie sind im Home-Verzeichnis des Users, navigieren Sie in das Root-Verzeichnis des Servers mit:
```Shell
$ cd ..
```
Nun installieren Sie über den bereits vorhandenen Package-Manager yum die benötigte Java Version:

Für Java 1.8
```Shell
$ yum install java-1.8.0-openjdk-devel
```
oder für Java 1.7
```Shell
$ yum install java-1.7.0-openjdk-devel
```
Um die Installation zu bestätigen, führen Sie den Befehl

```Shell
$ /usr/sbin/alternatives --config java
```
![JavaWahl](./Bilder/JavaWahl.png)

und besätigen Sie ihre Auswahl. Liegt nur eine Java Installation vor, enthält die List nur einen Eintrag.
Denselben Vorgang wiederholen Sie für den Befehl
```Shell
$ /usr/sbin/alternatives --config javac
```
#### Maven
Um [Maven](http://maven.apache.org/download.cgi) zu installieren, downloaden Sie [Maven](http://maven.apache.org/download.cgi) auf ihren lokalen Rechner. Wählen Sie dabei den im bild gezeigten Download-Link unter ***Binary Zip archive***.

![MavenDownload](./Bilder/MavenDownload.png)

und transferieren Sie die Zip-Datei per FileZilla in das Home-Verzeichnis ihres Users (/home/user/apache-maven-3.5.4-bin). Navigieren Sie über Putty in diesem Ordner, entpacken Sie die Zip-Datei und löschen Sie diese anschließend.
```Shell
$ cd home/user/
$ unzip apache-maven-3.5.4-bin.zip
$ rm apache-maven-3.5.4-bin.zip
```
Nun öffnen Sie ihr Bash_Profile mit dem Texteditor vim
```Shell
$ vi ~/.bash_profile
```
und tragen Sie die Variablen
```Shell
export M2_HOME=/home/user/apache-maven-3.5.4
export M2=$M2_HOME/bin
export PATH=$M2:$PATH
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
```
ein. Beispielhaft:

![bashprofile](./Bilder/bashprofile.png)

Beenden Sie den Editor, indem Sie ***ESC*** drücken und ***:wq!*** eingeben und mit ***Enter*** bestätigen. Damit die Änderung in Kraft tritt, führen Sie folgenden Befehl aus:
```Shell
$ source ~/.bash_profile
```
Um die Installation zu überprüfen, geben Sie
```Shell
$ mvn --version
```
ein.
### Wildfly mit Teiid
[Externer Guide](https://docs.jboss.org/author/display/WFLY10/Getting+Started+Guide)
[Externer Guide](https://access.redhat.com/documentation/en-us/red_hat_jboss_data_virtualization/6.3/html/installation_guide/ch04)
[Hier](http://teiid.io/teiid_runtimes/teiid_wildfly/downloads/) [(interner Link)](https://infocloud.infomotion.de/index.php/apps/files/?dir=/Teiid%20Dokumentation&fileid=4750825) finden Sie eine Wildfly 11 Version mit bereits installierem Teiid 11.0.1. Downloaden Sie dafür die Zip-Datei unter dem Link (with Wildfly/Console)

![WildflyTeiid](./Bilder/WildflyTeiid.png)

Verschieben Sie diese Datei ebenfalls mit FileZilla in den Home-Ordner ihres Users (/home/user/teiid-11.0.1-wildfly-server.zip) und entpacken bzw. löschen Sie anschließend den Zip-Ordner.

```Shell
$ unzip teiid-11.0.1-wildfly-server.zip
$ rm teiid-11.0.1-wildfly-server.zip
```

Fügen Sie in ihr Bash_Profile die Zeile
```Shell
export JBOSS_HOME=/home/user/teiid-11.0.1
```
ein. Benuten Sie eine andere Version, benennen Sie den Pfad dementsprechend. Geben Sie wieder

```Shell
$ source ~/.bash_profile
```

ein, um die Änderung aktiv werden zu lassen.
Wildfly mit Teiid ist nur erfolgreich auf Ihrem System.
#### Freigabe von Ports
Wildfly ermöglicht die Administration des Servers über eine Weboberfläche. Damit alle Funktionen von Wildfly verwendet werden können, müssen auf dem Server bestimmte Ports freigegeben werden. Führen Sie dafür die Befehle:
```Shell
$ sudo firewall-cmd --add-port 8787/tcp
$ sudo firewall-cmd --add-port 9990/tcp
$ sudo firewall-cmd --add-port 31000/tcp
```
aus. Um die offenen Ports aufzulisten:
```Shell
$ sudo firewall-cmd --list-ports
```
***8787***
 Wird für Debugzwecke verwendet.
***9990***
Port für Managementservices. Ist dieser Port nicht offen, kann das Developer Studio einen gestarteten Server nicht erkennen und die Weboberfläche ist nicht verfügbar.
***31000***
Dies ist ein Port, der die Schnittstelle zu Teiid bildet. Ist dieser nicht offen, wird keine laufende Instanz von Teiid erkannt.
#### Erklärung der Ordnerstruktur
In diesem Abschnitt gebe ich Ihnen einen kleinen Überblick über die vorhandene Ordnerstruktur und benenne die wesentlichen Dateien, die für die weitere Konfiguration des Wildfly-Servers von Bedeutung sein werden.
Die relevanten Ordner sind ***bin***, ***modules*** und ***standalone***

$ teiid-11.0.1
.
├── ***bin***
│  &nbsp;&nbsp;&nbsp; ├── jboss-cli.sh
│  &nbsp;&nbsp;&nbsp; ├── add-user.sh
│	&nbsp;&nbsp;&nbsp;	├── standalone.sh
│  &nbsp;&nbsp;&nbsp;	├── ...
├── ***modules***
│  &nbsp;&nbsp;&nbsp; └── system
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 		├── layers
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 				└── ...
├── ***standalone***
│  &nbsp;&nbsp;&nbsp; └── configuration
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 		├── standalone-teiid.xml
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 		├── application-users.properties
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 		├── application-roles.properties
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 		├── mgmt-users.properties
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 		├── mgmt-groups.properties
│  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 		└── ...
│  &nbsp;&nbsp;&nbsp; ├── ...
├── ...

Alle hier aufgelisteten Datein und Ordner sind von Relevanz und werden im folgenden näher erklärt.
##### bin
Im ***bin*** Ordner befinden sich die ausführbaren Datein. Die ***jboss-cli.sh*** ist das Skript, mit welchem das ***C***ommand***L***ine***I***nterface des Wildfly-Server aufgerufen werden kann. Genau Benutzung wird später erklärt. ***add-user.sh*** startet das Skript zum Hinzufügen eines Users und ***standalone.sh*** startet den Server.
##### modules
Im ***modules***-Ordner befinden sich alle möglichen Module des Servers. Darunter befinden sich ebenfalls die Treiber für Datenbankschnittstellen, wofür wir diesen Ordner im späteren Verlauf verwenden werden.
##### standalone
Der ***standalone***-Ordner beinhaltet die Configurationsdatei des Servers. Im Unterordner ***confuguration*** befindet sich die Datei ***standalone-teiid.xml***, aus beim Start des Servers die Einstellungen genommen werden. Die ****.properties***-Dateien besitzen Angaben zu den Users, sowie deren Rollen.
### JBoss Developer Studio mit Teiid Designer
Installieren Sie das [JBoss Developer Studio](https://developers.redhat.com/products/devstudio/overview/) [(interner Link)](https://infocloud.infomotion.de/index.php/apps/files/?dir=/Teiid%20Dokumentation&fileid=4750825) auf ihrer lokalen Maschine und downloaden Sie den [teiid-Designer](http://teiiddesigner.jboss.org/designer_summary/downloads) [(interner Link)](https://infocloud.infomotion.de/index.php/apps/files/?dir=/Teiid%20Dokumentation&fileid=4750825). Hierbei handelt es sich um ein Plugin für das JBoss Developer Studio. Öffnen Sie das installierte JBoss Developer Studio und klicken Sie auf den Reiter ***Help*** und klicken Sie auf ***Install new Software...***

![installNewSoftware](./Bilder/installNewSoftware.png)

In diesem Fenster klicken Sie rechts auf ***Add...***

![softwareAdd](./Bilder/softwareAdd.png)

im geöffneten Fenster klicken Sie auf ***Archive*** und wählen die .zip-Datei des Teiid-Designers, die im vorherigen Schritt heruntergeladen wurde.

![addTeiiddesigner](./Bilder/addTeiiddesigner.png)

Klicken Sie auf ***Öffnen*** und vergeben Sie einen beliebigen Namen

![addTeiiddesigner2](./Bilder/addTeiiddesigner2.png)

und bestätigen Sie mit ***OK***.

![addDatavirtualization](./Bilder/addDatavirtualization.png)

Wählen Sie nun ***Data Virtualization*** aus und klicken Sie auf ***Next***. Die benötigten Komponenten werden nun installiert. Das Developerstudio ist nun erfolgreich installiert.
## Benutzung des Applikationsservers
### Verbinden von JBoss Developer Studio mit dem Wildfly-Server
[Externer Guide](https://tools.jboss.org/documentation/howto/configure_remote_server.html)
Wir haben nun den Wildfly-Server auf unserem Server installiert und haben das Developer Studio lokal auf unserem Rechner. Nun verbinden wir das Developer Studio mit unserem Server. Öffnen Sie dafür die ***Remote System***-Ansicht. Diese ist in der Menüleiste unter Window -> Show View -> Other zu finden.

![remoteSystem](./Bilder/remoteSystem.png)

Dort öffnen Sie Remote Systems -> Remote Systems. Legen Sie dort eine neue Verbindung an und wählen Sie ***SSH Only***.

![sshonly](./Bilder/sshonly.png)

Tragen Sie dort unter ***Host name*** die IP-Adresse des Servers ein und vergeben Sie einen beliebigen ***Connection name***.

![remoteip](./Bilder/remoteip.png)

Nun haben Sie Zugriff auf das Filesystem ihres Servers über das Developer Studio. Ab diesem Zeitpunkt wird FileZilla nicht mehr benötigt, da alle Filetransfers nun über das Developer Studio stattfinden können. Öffnen Sie nun unter Window -> Perspective -> Open Perspective -> Other... den Teiid Designer.

![teiiddesignerperspective](./Bilder/teiiddesignerPerspective.png)

Am rechten Rand gehen Sie nun auf den Reiter ***Servers***. Legen Sie per Rechtsklick -> new -> Server einen neuen Server an. Wählen Sie in der Kategorie ***JBoss Community*** ihre Serverversion, in unserem Fall Wildfly 11.

![wildlfy11](./Bilder/wildfly11.png)

Tragen Sie im Feld ***Server's host name*** ihren eben erstellen Host ein und vergeben Sie einen beliebigen ***Server name***.

![remoteRuntime](./Bilder/remoteRuntime.png)

Nun stellen Sie bei ***The server is*** Remote ein und ***Controlled by*** auf Filesystem and shell operations. Wählen Sie ***Assign a runtime to this server*** ab. Ob ein Haken bei ***Server lifecycle is externally managed*** hängt davon ab, ob Sie den Applikationsserver über das Developer Studio starten wollen oder über die Shell auf dem Server direkt. Es wird ermpfohlen den Haken nicht zu setzen und die letztere Variante zu wählen. Dazu später mehr.
Im nächsten Fenster wählen Sie ihren Host aus und klicken auf ***Browse...*** neben ***Remote Server Home***.

![hostconfiguration](./Bilder/hostconfiguration.png)

Wählen Sie dort den Home-Server ihres gewählen Applikationsservers aus und bestätigen Sie.

![teiidwahl](./Bilder/teiidwahl.png)

Nun wählen Sie ***Browse...*** hinter ***Remote Server Configuration File***. Navigieren Sie dort in standalone -> configuration und wählen Sie die ***standalone-teiid.xml***.

![confwahl](./Bilder/confwahl.png)

Und beenden Sie mit ***Finish***.

![conffinish](./Bilder/conffinish.png)

Nun können Sie im Reiter ***Servers*** ihren konfigurieten Server finden.

![wildfly11server](./Bilder/wildfly11server.png)

Doppelklicken Sie ihren Server, um das Fenster für die Servereinstellungen zu öffnen.

![serverconf](./Bilder/serverconf.png)

Um sich nun mit dem Wildfly-Server verbinden zu können, muss ein Managementuser erstellt werden, dessen Credentials in ***User Name*** und ***Password*** eingetragen werden.
### User anlegen
[Externer Guide](https://docs.jboss.org/author/display/WFLY8/add-user+utility)
Es gibt zwei Arten von Usern:
- Managementuser
- Applikationsuser

Hierfür wird das Skript ***add-user.sh*** verwendet, das früher bereits angesprochen wurde. Managementuser werden für alles verwendet, was mit der Serverkonfiguration zu tun hat und Applikationsuser für alles was mit Teiid und Datenmodellierung zusammenhängt. Wir werden im folgenden für beide Klassen einen User anlegen. Navigieren Sie dafür über die Shell in Putty zum Ordner der die ***add-user.sh*** beinhaltet ($TEIID_HOME/bin) und starten Sie das Skript:
```Shell
$ sh add-user.sh
```
![addmanuser](./Bilder/addmanuser.png)

Wählen Sie dort mit ***a*** einen Management User und geben Sie einen beliebigen Username und Password ein. Überspringen Sie mit Enter die Vergabe einer Gruppe und geben Sie anschließend ***no*** ein. Damit ist ein Managementuser erfolgreich angelegt. Zum Anlegen eines Applikationsusers wählen Sie im ersten Schritt ***b*** und folgen Sie den weiteren Schritten. Wir werden im weiteren einen Applikationsuser mit dem Namen ***teiidUser*** verwenden.
Tragen Sie nun die die Logindetails des Managementusers im Developer Studio ein. Damit kann mit einer laufenden Instanz des Applikationsservers verbunden werden. Wechseln Sie am unteren Rand auf ***Teiid Instance***.

![teiidlogin](./Bilder/teiidlogin.png)

Tragen Sie dort die Credentials des Applikationsusers ein. Klicken Sie auf ***Test Administation Connection*** und ***Test JDBC Connection***. Erscheint ein ***OK*** ist alles richtig konfiguriert. Nun muss der Server gestartet werden und wir können uns über das Developer Studio mit der laufenden Instanz verbinden.

### Starten des Servers
[Externer Guide](http://blog.arungupta.me/bind-wildfly-different-ip-address-multihomed/)
Um den Server zu starten gibt es zwei Möglichkeiten:
- Über das Developerstudio
- Über die Shell

Es wird empfohlen die zweite Variante zu wählen. Hier haben wir die Möglichkeit die zu erreichende IP-Adresse anzugeben und den Prozess des Applikationsservers ggfs. in den Hintergrund zu packen, damit die bestehende SSH-Verbindung unterbrochen werden kann und der Server weiterläuft. Für ersteres muss ***Server lifecycle is externally managed*** abgewählt werden.
#### Starten über das Developer Studio
Um so den Server zu starten, wählen Sie den Server im Developer Studio aus und klicken Sie auf Start. Der Server ist nun gestartet und Sie sind direkt mit der laufenden Instanz verbunden. Schließen Sie nun das Developer Studio wird auch der Server heruntergefahren. Aus diesem Grund wird die Variante mit der Shell empfohlen.

#### Starten über die Shell
Hier wird beschrieben, wie der Server über die Shell gestartet werden kann. Dabei wird der Server einmal normal gestartet und einmal im Hintergrund.
##### Im Vordergrund
Verbinden Sie sich mit dem Server über Putty und navigieren Sie in den ($TEIID_HOME/bin)-Ordner. Dort liegt das Skript ***standalone.sh***, welches den Server startet. Beim starten geben wir dem Server jedoch weitere Einstellungen mit.
```Shell
$ sh standalone.sh -c standalone-teiid.xml -b 172.16.20.38 -bmanagement 172.16.20.38
```
Dies startet den Server mit der Konfigurationsdatei ***standalone-teiid.xml***. Wird diese Datei nicht angegeben, wird standardmäßig ***standalone.xml*** verwendet. Hiermit ist aber keine Teiid-Instanz möglich, welcher für die Datenvirtualisierung benötigt wird. ***-b*** und ***-bmanagement*** erhalten die IP-Adresse des Servers. Werden diese nicht angegeben, wird der Server auf localhost gestartet und wir erhalten von außerhalb keinen Zugriff auf den Applikationsserver. Um sich nun mit der laufenden Instanz zu verbinden, wählen Sie im Developer Studio den Server aus und klicken Sie start. Stellen Sie sich, dass ***Server lifecycle is externally managed*** angewählt ist. Schließen Sie nun ihren Putty-Session wird auch der Applikationsserver beendet. Deshalb kann der Server auch im Hintergrund gestartet werden.
##### Im Hintergrund
Um den Prozess von der Shell zu koppeln muss
```Shell
$ nohup sh standalone.sh -c standalone-teiid.xml -b 172.16.20.38 -bmanagement 172.16.20.38 &
```
eingegeben werden. Der bisherige Befehl wird um ***nohup*** am Anfang erweitert und hinten wird ein ***&*** angefügt. Nun kann die SSH-Session geschlossen werden und der Server läuft trotzdem weiter.
#### Beenden über die Shell
Je nachdem, wie der Applikationsserver gestartet wurde, muss er auch wieder beendet werden.
##### Im Vordergrund
Wurde der Applikationsserver im Vordergrund, also nicht im Hintergrund gestartet, kann er mit der Tastenkombination ***STRG+C*** beendet werden.
##### Im Hintergrund
Wurde der Prozess in der Hintergrund gelegt, werden ein Paar mehr Schritte benötigt, um den Server wieder zu beenden. Navigieren Sie dazu wieder in den ($TEIID_HOME/bin)-Ordner und starten Sie das Skript ***jboss-cli.sh***
```Shell
$ sh jboss-cli.sh
```
geben Sie nun
```Shell
 [disconnected /] connect --controller=172.16.20.38
 ```
mit ihrer entsprechenden IP-Adresse ein. Sie sind nun mit dem laufenden Prozess verbunden. Mit ***shutdown*** fahren Sie den Applikationsserver nun herunter.
```Shell
 [standalone@172.16.20.38:9990 /] shutdown
 ```
 Verlassen Sie die Ansicht mit ***STRG+C***.
### Öffnen der Web Management Console
Ist der Server gestartet, kann sich mit der Web Management Console verbunden werden. Diese läuft im Regelfall unter dem Port 9990. Sie erreichen diese, indem Sie in ihrem Browser die IP-Adresse ihres Server auf dem Port 9990 öffnen. In unserem Beispiel unter: http://172.16.20.38:9990.
Melden Sie sich dort mit den Credentials des erstellten Managementusers an.
## Verbinden mit einer Datenquelle
Nun liegt ein gestarteter Server vor, mit dessen Instanz Sie über das Developer Studio verbunden sind. Im anschlißenden wird Ihnen gezeigt, wie Sie sich mit diversen Datenquellen verbinden können.
### Oracle
[Externer Guide](http://www.adam-bien.com/roller/abien/entry/installing_oracle_jdbc_driver_on)
Zum verbinden mit einer Oracle-Datenbank liegt kein installiertes Modul vor. Aus diesem Grund müssen wir die JDBC treiben per Hand auf dem Server installieren. Hier kommt der bereits angesprochene ***modules*** Ordner ins Spiel. Dort werden die benötigten Treiber abgelegt. Wildfly setzt dafür eine genau definierte Ordnerstruktur vor. Öffnen Sie dafür im Developer Studio wieder das ***Remote Systems*** Fenster. Navigieren Sie in den Ordner ihrer Wildflyinstallation und öffnen Sie den ***modules***-Ordner. Erstellen Sie dort die Ordner oracle -> jdbc -> main.

![ojdbc7](./Bilder/ojdbc7.png)

 In diesen Ordner legen sie den JDBC-Treiber [odjbc7.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-drivers-12c-download-1958347.html) [(interner Link)](https://infocloud.infomotion.de/index.php/apps/files/?dir=/Teiid%20Dokumentation&fileid=4750825).
 Zusätzlich wird eine ***module.xml***-Datei angelegt. Es ist wichtig, dass die Datei diesen Namen besitzt, da sie sonst nicht automatisch erkannt wird. Der Inhalt der Datei wird hier gezeigt:
 ```xml
 <?xml version="1.0" ?>

<module xmlns="urn:jboss:module:1.1" name="oracle.jdbc">

    <resources>
        <resource-root path="ojdbc7.jar"/>
    </resources>

    <dependencies>
        <module name="javax.api"/>
        <module name="javax.transaction.api"/>
    </dependencies>
</module>
 ```
 Sie referenziert die verwendete JDBC-Datei sowie weitere bereits installierte Abhängigkeiten und erhält einen eindeutigen Namen ```oracle.jdbc```. Nun ist das Modul angelegt und muss in der ***standalone-teiid.xml*** ($TEIID_HOME/bin/configuration/) referenziert werden. Öffnen Sie dafür die Datei im Text-Editor und suchen sie die Treiber ```<drivers>```.

 ![drivers](./Bilder/drivers.png)

 Fügen Sie dort die Zeile
 ```xml
<driver name="OracleJDBCDriver" module="oracle.jdbc"/>
 ```
 ein. module referenziert das eben erstellte Modul und geben dem Treiber den Namen ```OracleJDBCDriver```. Unter diesem Namen ist nun der Treiber bekannt. Jetzt können wir uns mit einer Oracle-Datenbank verbinden. Dafür suchen wir ebenfalls in ***standalone-teiid.xml*** die ```<datasources>``` und fügen eine neue hinzu.

![datasource](./Bilder/datasource.png)

```xml
<datasource jta="true" jndi-name="java:/OracleSchulungSNDev" pool-name="OracleSchulungSNDev" enabled="true" spy="true" use-ccm="true" statistics-enabled="true">
	<connection-url>jdbc:oracle:thin:@srv-im-etl.intern.infomotion.de:1521/pdb01d</connection-url>
  <driver-class>oracle.jdbc.OracleDriver</driver-class>
  <driver>OracleJDBCDriver</driver>
  <security>
  	<user-name>dbs_dwh_cw</user-name>
  	<password>dbs_dwh_cw</password>
  </security>
</datasource>
```
Diese Datasource verwendet den von uns angelegten ```<driver>``` ```OracleJDBCDriver```. Der ```jndi-name``` muss mit ***java:/*** beginnen. Dahinter kommt ein beliebiger Name, unter welchem unsere Datasource zu erreichen ist. Die ```<connection-url>``` beinhaltet die Adresse der Oracledatenbank, mit der sich verbunden werden will, sowie unter ```<security>``` die Anmeldedaten der Datenbank. Damit diese Änderung aktiv ist, muss der Applikationsserver neu gestartet werden. Ist der Applikationsserver neu gestartet, öffnen Sie die Weboberfläche der Managementconsole und navigieren Sie zu Subsystems -> Datasources -> Non XA -> ***Ihre Datasource*** und klicken auf ***View***.

![datasourceweb](./Bilder/datasourceweb.png)

Wählen Sie nun den Reiter ***Connection*** und klicken Sie auf ***Test Connection***.

![connectiontest](./Bilder/connectiontest.png)

Erhalten Sie folgendes Fenster, ist die Datasource erfolgreich konfiguriert und die Daten der Datenbank stehen zur weiteren Verarbeitung zur Verfügung.

![connectionsuccess](./Bilder/connectionsuccess.png)


### MongoDB
[Externer Guide](http://www.mastertheboss.com/javaee/mongodb/3-ways-you-can-connect-to-mongodb-from-wildfly-jboss-eap)
### Hive
[Externer Guide](https://developer.jboss.org/wiki/ConnectToAHadoopSourceUsingHive2)
### Excel
[Externer Guide](https://developer.jboss.org/wiki/MicrosoftExcelDocumentIntoRelationalTable)
### XML
[Externer Guide](https://docs.jboss.org/author/display/teiidexamples/Model+Remote+XML+File+Data+Source)
