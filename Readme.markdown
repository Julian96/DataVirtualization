# Dokumentation Teiid Daten Virtualisierung
## Inhaltsverzeichnis

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Dokumentation Teiid Daten Virtualisierung](#dokumentation-teiid-daten-virtualisierung)
	- [Inhaltsverzeichnis](#inhaltsverzeichnis)
	- [1. Einleitung](#1-einleitung)
		- [1.1 Verwendete Technologien](#11-verwendete-technologien)
		- [1.2 Grundlagen](#12-grundlagen)
		- [1.3 Kompatibilität](#13-kompatibilität)
	- [2. Installation](#2-installation)
		- [2.1 Verbinden per Putty](#21-verbinden-per-putty)
		- [2.2 Verbinden per FileZilla](#22-verbinden-per-filezilla)
		- [2.3 Installieren von Java und Maven](#23-installieren-von-java-und-maven)
			- [2.3.1 Java](#231-java)
			- [2.3.2 Maven](#232-maven)
		- [2.4 Wildfly mit Teiid](#24-wildfly-mit-teiid)
			- [2.4.1 Freigabe von Ports](#241-freigabe-von-ports)
			- [2.4.2 Erklärung der Ordnerstruktur](#242-erklärung-der-ordnerstruktur)
				- [2.4.2.1 bin](#2421-bin)
				- [2.4.2.2 modules](#2422-modules)
				- [2.4.2.3 standalone](#2423-standalone)
		- [2.5 JBoss Developer Studio mit Teiid Designer](#25-jboss-developer-studio-mit-teiid-designer)
	- [3. Benutzung des Applikationsservers](#3-benutzung-des-applikationsservers)
		- [3.1 Verbinden von JBoss Developer Studio mit dem Wildfly-Server](#31-verbinden-von-jboss-developer-studio-mit-dem-wildfly-server)
		- [3.2 User anlegen](#32-user-anlegen)
		- [3.3 Starten des Servers](#33-starten-des-servers)
			- [3.3.1 Starten über das Developer Studio](#331-starten-über-das-developer-studio)
			- [3.3.2 Starten über die Shell](#332-starten-über-die-shell)
				- [3.3.2.1 Im Vordergrund](#3321-im-vordergrund)
				- [3.3.2.2 Im Hintergrund](#3322-im-hintergrund)
			- [3.3.3 Beenden über die Shell](#333-beenden-über-die-shell)
				- [3.3.3.1 Im Vordergrund](#3331-im-vordergrund)
				- [3.3.3.2 Im Hintergrund](#3332-im-hintergrund)
		- [3.4 Öffnen der Web Management Console](#34-öffnen-der-web-management-console)
	- [4. Verbinden mit einer Datenquelle](#4-verbinden-mit-einer-datenquelle)
		- [4.1 Oracle](#41-oracle)
		- [4.2 MongoDB](#42-mongodb)
		- [4.3 Hive](#43-hive)
		- [4.4 Excel](#44-excel)
		- [4.5 XML](#45-xml)
	- [5. Modellieren von Daten](#5-modellieren-von-daten)
		- [5.1 Oracle](#51-oracle)
		- [5.2 Hive](#52-hive)
		- [5.3 View](#53-view)
	- [6. Erstellung der VDB](#6-erstellung-der-vdb)
		- [6.1 Data Roles#](#61-data-roles)
- [6.2 Testen der VDB](#62-testen-der-vdb)
	- [7. Verbinden mit VDB](#7-verbinden-mit-vdb)
		- [7.1 Tableau](#71-tableau)

<!-- /TOC -->

## 1. Einleitung
In dieser Dokumentation wird die Installation der Teiid Daten Virtualisierung beschrieben, sowie das Aufsetzen des Servers und das Verbinden mit diversen Datenquellen. Zudem werden generelle Grundlagen der Verwendung des Wildfly Servers und Verwendung der JBoss Developer Studio Oberfläche geklärt. Der Ordner mit benötigten Dateien ist [hier](https://infocloud.infomotion.de/index.php/apps/files/?dir=/Teiid%20Dokumentation&fileid=4750825) zu finden.
Die folgende Grafik zeigt einen Überblick über das gesamte System, das wir im Laufe dieser Dokumentation aufbauen und deren Schnittstellen.

![Schritt0](./Bilder/Schritt0.png)
### 1.1 Verwendete Technologien
- **Rechner 1(Server)**
	- OS: [Red Hat Enterprise Linux](https://www.redhat.com/de/technologies/linux-platforms/enterprise-linux)
	- Applikationsserver: [Wildfly](http://www.wildfly.org/) (Version 11)
	- Datenvirtualisierung: [Teiid](http://teiid.io/teiid_runtimes/teiid_wildfly/downloads/) (Version 11.0.1)
	- [Java 1.8](https://java.com/de/download/)
- **Rechner 2(User)**
	- [Red Hat JBoss Developer Studio](https://developers.redhat.com/products/devstudio/overview/) mit [Teiid Designer](http://teiid.io/tools/teiid_designer/) (Version 11.1.2)
	- [Putty](https://www.putty.org/)
	- [FileZilla](https://filezilla-project.org/)

### 1.2 Grundlagen
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


### 1.3 Kompatibilität
Ein bestehendes Problem sind die vielen zu Verfügung stehenden Versionen von Teiid und Wildfly, sowie des Teiid Designers. Die neuste Version von Teiid ist 11.0.1 (Stand 07.08.2018) und ist mit Wildfly 11 kompatibel. Die neuste Version von Wildfly ist Wildfly 13, jedoch gibt das hierfür keine Teiid Version. Zudem ist der Teiid Designer 11.1.2 nur vollständig kompatibel und getestet mit Teiid Version 9, was ebenfalls Wildfly 9 benötigt.

| Wildfly | Teiid  | Teiid Designer | Java |
|---------|--------|----------------|------|
| 13      | -      | -              | 1.8  |
| 11      | 11.0.1 | -              | 1.8  |
| 9       | 9.3.7  | 11.1.2         | 1.7  |

Also ergeben sich zwei Möglichkeiten. Entweder es wird Wildfly 9 mit Teiid 9.3.7 und dem Teiid Designer 11.1.2 verwendet. Dies ist eine etwas veraltete Variante, jedoch funktioniert der Teiid Designer vollständig. Oder es wird Wildfly 11 mit Teiid 11.0.1 und ebenfalls dem Teiid Designer 11.1.2, jedoch können hier Probleme mit der Oberfläche im Teiid Designer entstehen und es muss für bestimmte Tasks ggfs. auf die Command-Line gewechselt werden. Die Java Version ist bedingt durch die verwendeten Komponenten und wird bei der späteren Installation eine Rolle spielen.

***Im Folgenden wird Wildfly 11 mit Teiid 11.0.1 und der Teiid Designer 11.1.2 verwendet***

## 2. Installation
Dieses Kapitel beschäftigt sich mit der Installation der genannten Komponenten sowie deren Konfiguration zur weiteren Benutzung. Es wird davon ausgegangen, dass bereits ein Server mit dem Betriebssystem [Red Hat Enterprise Linux](https://www.redhat.com/de/technologies/linux-platforms/enterprise-linux) vorliegt. Diese Maschine sollte per SSH zu erreichen sein.
Im Kontext des Gesamtsystems setzen wir hier den rot markierten Bereich auf und die Verbindung von unserer lokalen Maschine dazu.
![Schritt1](./Bilder/Schritt1.png)

### 2.1 Verbinden per Putty
Um auf das Filesystem und die Shell des Servers zuzugreifen benötigen wir [Putty](https://www.putty.org/). Installieren Sie dies auf ihrer lokalen Maschine und starten Sie Putty.

![PuttyStart](./Bilder/PuttyStartscreen.png)

Stellen Sie den ***Connection type*** auf ***SSH*** um und tragen Sie in das Feld ***Host Name*** die IP-Adresse des Servers ein sowie den ***Port*** (Standardmäßig Port: 22). Klicken Sie anschließend auf ***Load***.
Nun öffnet sich die Shell des Servers.

![ShellStart](./Bilder/ShellStartscreen.png)

Melden Sie sich mit den Benutzerdaten an. Hier ggfs. den Systemadministrator fragen.
### 2.2 Verbinden per FileZilla
[FileZilla](https://filezilla-project.org/) wird für den Anfang benötigt, um den Ordner mit dem Applikationsserver auf den Server zu verschieben. Hierfür installieren Sie [FileZilla](https://filezilla-project.org/) und starten Sie die Anwendung.

Öffnen Sie über Datei -> Servermanager die Serververwaltung legen Sie einen neuen Server an.

![FileZillaServermanager](./Bilder/FileZillaServermanager.png)

Stellen Sie das ***Protokoll*** auf SFTP um und tragen Sie die IP-Adresse in das Feld ***Server***. Ebenfalls tragen Sie ihren Benutzer und das Passwort in die vorgesehenen Felder ein und klicken Sie auf verbinden. Nun haben Sie Zugriff auf das Filesystem des Servers.

### 2.3 Installieren von Java und Maven
Java und Maven werden als Installation auf dem Server benötigt, damit Wildlfy und Teiid funktionieren.
#### 2.3.1 Java
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
#### 2.3.2 Maven
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
### 2.4 Wildfly mit Teiid
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
#### 2.4.1 Freigabe von Ports
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
#### 2.4.2 Erklärung der Ordnerstruktur
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
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 	  	├── layers  
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 	  			└── ...  
├── ***standalone***  
│  &nbsp;&nbsp;&nbsp; └── configuration  
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 	  	├── standalone-teiid.xml  
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   		├── application-users.properties  
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   		├── application-roles.properties  
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 	  	├── mgmt-users.properties  
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 	  	├── mgmt-groups.properties  
│    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 	  	└── ...  
│  &nbsp;&nbsp;&nbsp; ├── ...  
├── ...  

Alle hier aufgelisteten Datein und Ordner sind von Relevanz und werden im folgenden näher erklärt.
##### 2.4.2.1 bin
Im ***bin*** Ordner befinden sich die ausführbaren Datein. Die ***jboss-cli.sh*** ist das Skript, mit welchem das CommandLineInterface des Wildfly-Server aufgerufen werden kann. Genau Benutzung wird später erklärt. ***add-user.sh*** startet das Skript zum Hinzufügen eines Users und ***standalone.sh*** startet den Server.
##### 2.4.2.2 modules
Im ***modules***-Ordner befinden sich alle möglichen Module des Servers. Darunter befinden sich ebenfalls die Treiber für Datenbankschnittstellen, wofür wir diesen Ordner im späteren Verlauf verwenden werden.
##### 2.4.2.3 standalone
Der ***standalone***-Ordner beinhaltet die Configurationsdatei des Servers. Im Unterordner ***confuguration*** befindet sich die Datei ***standalone-teiid.xml***, aus beim Start des Servers die Einstellungen genommen werden. Die ****.properties***-Dateien besitzen Angaben zu den Users, sowie deren Rollen.
### 2.5 JBoss Developer Studio mit Teiid Designer
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
## 3. Benutzung des Applikationsservers
### 3.1 Verbinden von JBoss Developer Studio mit dem Wildfly-Server
[Externer Guide](https://tools.jboss.org/documentation/howto/configure_remote_server.html)  
Wir haben nun den Wildfly-Server auf unserem Server installiert und haben das Developer Studio lokal auf unserem Rechner. Nun verbinden wir das Developer Studio mit unserem Server. Öffnen Sie dafür die ***Remote System***-Ansicht. Diese ist in der Menüleiste unter Window -> Show View -> Other zu finden.

![remoteSystem](./Bilder/remoteSystem.png)

Dort öffnen Sie Remote Systems -> Remote Systems. Legen Sie dort eine neue Verbindung an und wählen Sie ***SSH Only***.

![sshonly](./Bilder/sshonly.png)

Tragen Sie dort unter ***Host name*** die IP-Adresse des Servers ein und vergeben Sie einen beliebigen ***Connection name***.

![remoteip](./Bilder/remoteip.png)

Nun haben Sie Zugriff auf das Filesystem ihres Servers über das Developer Studio. Ab diesem Zeitpunkt wird FileZilla nicht mehr benötigt, da alle Filetransfers nun über das Developer Studio stattfinden können. Öffnen Sie nun unter Window -> Perspective -> Open Perspective -> Other... den Teiid Designer.

![teiiddesignerperspective](./Bilder/teiiddesignerperspective.png)

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
### 3.2 User anlegen
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

### 3.3 Starten des Servers
[Externer Guide](http://blog.arungupta.me/bind-wildfly-different-ip-address-multihomed/)  
Um den Server zu starten gibt es zwei Möglichkeiten:
- Über das Developerstudio
- Über die Shell

Es wird empfohlen die zweite Variante zu wählen. Hier haben wir die Möglichkeit die zu erreichende IP-Adresse anzugeben und den Prozess des Applikationsservers ggfs. in den Hintergrund zu packen, damit die bestehende SSH-Verbindung unterbrochen werden kann und der Server weiterläuft. Für ersteres muss ***Server lifecycle is externally managed*** abgewählt werden.
#### 3.3.1 Starten über das Developer Studio
Um so den Server zu starten, wählen Sie den Server im Developer Studio aus und klicken Sie auf Start. Der Server ist nun gestartet und Sie sind direkt mit der laufenden Instanz verbunden. Schließen Sie nun das Developer Studio wird auch der Server heruntergefahren. Aus diesem Grund wird die Variante mit der Shell empfohlen.

#### 3.3.2 Starten über die Shell
Hier wird beschrieben, wie der Server über die Shell gestartet werden kann. Dabei wird der Server einmal normal gestartet und einmal im Hintergrund.
##### 3.3.2.1 Im Vordergrund
Verbinden Sie sich mit dem Server über Putty und navigieren Sie in den ($TEIID_HOME/bin)-Ordner. Dort liegt das Skript ***standalone.sh***, welches den Server startet. Beim starten geben wir dem Server jedoch weitere Einstellungen mit.
```Shell
$ sh standalone.sh -c standalone-teiid.xml -b 172.16.20.38 -bmanagement 172.16.20.38
```
Dies startet den Server mit der Konfigurationsdatei ***standalone-teiid.xml***. Wird diese Datei nicht angegeben, wird standardmäßig ***standalone.xml*** verwendet. Hiermit ist aber keine Teiid-Instanz möglich, welcher für die Datenvirtualisierung benötigt wird. ***-b*** und ***-bmanagement*** erhalten die IP-Adresse des Servers. Werden diese nicht angegeben, wird der Server auf localhost gestartet und wir erhalten von außerhalb keinen Zugriff auf den Applikationsserver. Um sich nun mit der laufenden Instanz zu verbinden, wählen Sie im Developer Studio den Server aus und klicken Sie start. Stellen Sie sich, dass ***Server lifecycle is externally managed*** angewählt ist. Schließen Sie nun ihren Putty-Session wird auch der Applikationsserver beendet. Deshalb kann der Server auch im Hintergrund gestartet werden.
##### 3.3.2.2 Im Hintergrund
Um den Prozess von der Shell zu koppeln muss
```Shell
$ nohup sh standalone.sh -c standalone-teiid.xml -b 172.16.20.38 -bmanagement 172.16.20.38 &
```
eingegeben werden. Der bisherige Befehl wird um ***nohup*** am Anfang erweitert und hinten wird ein ***&*** angefügt. Nun kann die SSH-Session geschlossen werden und der Server läuft trotzdem weiter.
#### 3.3.3 Beenden über die Shell
Je nachdem, wie der Applikationsserver gestartet wurde, muss er auch wieder beendet werden.
##### 3.3.3.1 Im Vordergrund
Wurde der Applikationsserver im Vordergrund, also nicht im Hintergrund gestartet, kann er mit der Tastenkombination ***STRG+C*** beendet werden.
##### 3.3.3.2 Im Hintergrund
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
### 3.4 Öffnen der Web Management Console
Ist der Server gestartet, kann sich mit der Web Management Console verbunden werden. Diese läuft im Regelfall unter dem Port 9990. Sie erreichen diese, indem Sie in ihrem Browser die IP-Adresse ihres Server auf dem Port 9990 öffnen. In unserem Beispiel unter: http://172.16.20.38:9990.
Melden Sie sich dort mit den Credentials des erstellten Managementusers an.
## 4. Verbinden mit einer Datenquelle
Nun liegt ein gestarteter Server vor, mit dessen Instanz Sie über das Developer Studio verbunden sind. Im anschließenden wird Ihnen gezeigt, wie Sie sich mit diversen Datenquellen verbinden können.
Im Kontext ist das folgender Bereich:

![Schritt2](./Bilder/Schritt2.png)

Zu jeder Datenquelle, mit der sich verbunden werden soll, muss der dementsprechende Treiber auf dem Applikationsserver vorliegen, hier in der Grafik JDBC Treiber genannt. Mit diesem wird eine Datasource erstellt, die nur Metadaten beinhaltet.
### 4.1 Oracle
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
Diese Datasource verwendet den von uns angelegten ```<driver>``` ```OracleJDBCDriver```. Der ```jndi-name``` muss mit ***java:/*** beginnen. Dahinter kommt ein beliebiger Name, unter welchem unsere Datasource zu erreichen ist. Die ```<connection-url>``` beinhaltet die Adresse der Oracledatenbank (Hinweis: Es muss die vollständige Adresse des Service-Names angegeben werden, wenn sich über diesen verbunden werden soll!), mit der sich verbunden werden will, sowie unter ```<security>``` die Anmeldedaten der Datenbank. Damit diese Änderung aktiv ist, muss der Applikationsserver neu gestartet werden. Ist der Applikationsserver neu gestartet, öffnen Sie die Weboberfläche der Managementconsole und navigieren Sie zu Subsystems -> Datasources -> Non XA -> ***Ihre Datasource*** und klicken auf ***View***.

![datasourceweb](./Bilder/datasourceweb.png)

Wählen Sie nun den Reiter ***Connection*** und klicken Sie auf ***Test Connection***.

![connectiontest](./Bilder/connectiontest.png)

Erhalten Sie folgendes Fenster, ist die Datasource erfolgreich konfiguriert und die Daten der Datenbank stehen zur weiteren Verarbeitung zur Verfügung.

![connectionsuccess](./Bilder/connectionsuccess.png)


### 4.2 MongoDB
[Externer Guide](http://www.mastertheboss.com/javaee/mongodb/3-ways-you-can-connect-to-mongodb-from-wildfly-jboss-eap)  
### 4.3 Hive
[Externer Guide](https://developer.jboss.org/wiki/ConnectToAHadoopSourceUsingHive2)  
Um uns mit einer Hive-Instanz zu erbinden, benötigen wir ebenfalls die dementsprechenden JDBC-Treiber. Da unsere Hive-Instanz im AWS läuft, benutzen wir die von Amazon angebotenen Treiber. Diese sind [hier](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/HiveJDBCDriver.html) zu finden.
Diese müssen ebenfalls im ***modules***-Ordner platziert werden, im Pfad $TEIID_HOME/modules/system/layers/dv/org/apache/hadoop/hive/main

![HiveTreiber1](./Bilder/HiveTreiber1.png)

Dazu wird wieder eine ***modules.xml*** angelegt mit folgendem Inhalt:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<module name="org.apache.hadoop.hive" xmlns="urn:jboss:module:1.0">
    <resources>
        <resource-root path="commons-codec-1.3.jar"/>
        <resource-root path="commons-logging-1.1.1.jar"/>
        <resource-root path="hive_metastore.jar"/>
        <resource-root path="hive_service.jar"/>
        <resource-root path="HiveJDBC41.jar"/>
        <resource-root path="httpclient-4.1.3.jar"/>
        <resource-root path="httpcore-4.1.3.jar"/>
        <resource-root path="libfb303-0.9.0.jar"/>
        <resource-root path="libthrift-0.9.0.jar"/>
        <resource-root path="log4j-1.2.14.jar"/>
        <resource-root path="ql.jar"/>
        <resource-root path="slf4j-api-1.5.11.jar"/>
        <resource-root path="slf4j-log4j12-1.5.11.jar"/>
        <resource-root path="TCLIServiceClient.jar"/>
        <resource-root path="zookeeper-3.4.6.jar"/>
    </resources>
    <dependencies>
        <module name="org.slf4j"/>
        <module name="org.apache.commons.logging"/>
        <module name="javax.api"/>
        <module name="javax.resource.api"/>
    </dependencies>
</module>
```
Hier werden alle abgelegten Treiberdateien referenziert. Unser Modul erhält den Namen ***org.apache.hadoop.hive***.
Nun muss dieer Treiber ebenfalls in der ***standalone-teiid.xml*** angegeben.
```xml
<driver name="hive" module="org.apache.hadoop.hive">
	<driver-class>com.amazon.hive.jdbc41.HS2Driver</driver-class>
</driver>
```
Der Treiber erhält den Namen **hive*** und die Treiberklasse ist ***com.amazon.hive.jdbc41.HS2Driver***
Mit diesem Treiber legen wir nun die passende Datasource an.
```xml
<datasource jta="true" jndi-name="java:/HadoopDS" pool-name="HadoopDS" enabled="true" use-ccm="true">
	<connection-url>jdbc:hive2://ec2-35-159-52-226.eu-central-1.compute.amazonaws.com:10000/default</connection-url>
	<driver-class>com.amazon.hive.jdbc41.HS2Driver</driver-class>
	<driver>hive</driver>
</datasource>
```
Die Connection-url beinhaltet die DNS unseres Amazon-EMR-Masters. Dort läuft Hive standardmäßig auf ***Port 10000***, angeführt von ***jdbc:hive2***.
Nun kann wie bei der Einrichtung der Oracle-Datasource überprüft werden, ob die Verbindung erfolgreich hergestellt wurde.
### 4.4 Excel
[Externer Guide](https://developer.jboss.org/wiki/MicrosoftExcelDocumentIntoRelationalTable)  
### 4.5 XML
[Externer Guide](https://docs.jboss.org/author/display/teiidexamples/Model+Remote+XML+File+Data+Source)  
## 5. Modellieren von Daten
In diesem Kapitel wird das Erstellen von Model und Views beschrieben, mit denen später eine VDB erzeugt wird. Im Kontext sind das die markierten Bereiche.

![Schritt3](./Bilder/Schritt3.png)

Um nun ein Metadaten-Model für die verschiedenen Quellen zu erzeugen, muss im Developer Studio ein neues Projekt angelegt werden.

![newModelProject](./Bilder/newModelProject.png)

Geben Sie einen Namen für das Projekt ein und bestätigen Sie.

![newModelProjectName](./Bilder/newModelProjectName.png)

Es wird ein Projekt angelegt mit folgender Ordnerstruktur:

![ProjectFolder](./Bilder/ProjectFolder.png)

In den Ordner ***sources*** werden die Modelle abelegt. Um solch ein Modell zu erzeugen und direkt mit der Datasource zu verbinden, drücken wir im Reiter ***Guides*** und ***Model Teiid Data Source*** auf ***Create source model from teiid data source DDL***.
![newModel](./Bilder/newModel.png)
### 5.1 Oracle
Um ein Modell mit der Oracle-Datasource zu erstellen, wählen Sie die Oracle-Datasource aus und klicken Sie auf ***next***.
![OracleModelDatasource](./Bilder/OracleModelDatasource.png)

Wählen Sie im nächsten Fenster als translator ***oracle*** und geben Sie im Feld ***Schema Pattern*** ***DBS_DWH_CW%*** ein. Das nimmt alle Schemas aus der Datenbank, die mit ***DBS_DWH_CW*** beginnen.

![OracleModelSettings](./Bilder/OracleModelSettings.png)

Klicken Sie auf ***next***, bennennen Sie das Model und wählen Sie einen Ordner zum Ablegen des Modells.

![OracleModelOrt](./Bilder/OracleModelOrt.png)

Klicken Sie auf ***next*** und wählen Sie die Tabellen zum importieren aus. Tritt dabei ein Fehler auf, dass die DDL nicht importiert werden kann, erstellen Sie lokal eine DDL dabei und kopieren Sie den angezeigten DDL-Text in diese Datei. Dort entfernen Sie in den Klammern der ***bigdecimal***-Variablen die Zahl hinter dem Komma, sowie das Komma. Entfernen Sie zudem die oberste Zeile, in der der Namespace gesetzt wird.
Um nun das Model zu erzeugen, klicken Sie mit Rechtsklick auf de ***sources***-Ordner in ihrem Projekt und klicken Sie ***import***.

![import](./Bilder/Import.png)

Dort wählen Sie unter Teiid Designer ***DDL File(Teiid)***.

![import2](./Bilder/Import2.png)

Klicken Sie auf ***Choose from file system*** und navigieren Sie zu der eben erstellten DDL-Daten und klicken Sie auf ***next***.

![import3](./Bilder/OracleDDLORt.png)

Nun können Sie in diesem Menü die gewünschten Tabellen auswählen.

![import4](./Bilder/importDDLTabellen.png)

Das Modell ist nun erfolgreich importiert.
### 5.2 Hive
Um ein Modell für eine Hive-Datasource zu erzeugen, beginnen Sie wie bei den Schritten zum Modell der Oracle-Datasource. Klicken Sie im Guides Fentster ebenfalls auf ***Create source model from teiid data source DDL*** und wählen Sie dort die Hive-Datasource.

![HiveDatasource](./Bilder/HadoopDatasource.png)

Klicken Sie auf ***next*** und stellen Sie den translator auf ***hive***.

![HiveTabellen](./Bilder/HiveTabellen.png)

Nach dem Import wählen Sie die gewünschten Tabellen und klicken Sie auf ***Finish***. Das Modell ist erfolgreich erstellt.

### 5.3 View

Um eine View zu Erzeugen, erstellen Sie ein neues Medadaten Modell. Stellen Sie jedoch den Model Type auf ***View Model*** und vergeben Sie einen Namen.

![View1](./Bilder/View1.png)

Rechtsklicken Sie auf das erzeugte Modell und fügen Sie ein neue Table an durch New Child -> Table...

![View2](./Bilder/View2.png)

Wählen Sie Option 1

![View3](./Bilder/View3.png)

und geben Sie im Reiter ***Transformation SQL*** das SQL-Statement ein.

![View4](./Bilder/View4.png)

Die View ist erfolgreich angelegt.
## 6. Erstellung der VDB
Die Erstellung der Modelle ist bisher nur lokal gewesen. Nun wir eine VDB mit diesem Modellen erzeugt, die anschließend auf dem Applikationsserver platziert wird. Aktuell befinden wir uns hier:

![Schritt35](./Bilder/Schritt35.png)

Um eine VDB zu erzeugen, rechtsklicken Sie auf einen Ordner im Projekt und wählen Sie unter new **Teiid VDB***.

![VDB1](./Bilder/VDB1.png)

Klicken Sie im nächsten Fenster auf ***Add*** und wählen Sie die erstellten Modelle und die View.

![VDB2](./Bilder/VDB2.png)

Beenden Sie das nächste Fenster mit ***Finish***.

![VDB3](./Bilder/VDB3.png)

Die VDB ist erfolgreich angelegt.

### 6.1 Data Roles#

Um Zugriffsbeschränkungen auf die VDB zu legen, werden Data Roles verwendet. Eine Data Role wird einer Applikationsuser Rolle zugeordnet. Jeder Applikationsuser, der dieser Role angehört, erhält die Beschränkungen, die auf der Data Role liegen. Diese können erstellt werden, indem Sie einen Doppelklick auf die eben erstellte VDB machen und auf den Reiter ***Data Roles*** wechseln.

![DataRole1](./Bilder/DataRole1.png)

Dort klicken Sie unten links auf das gezeigte Symbol.

![DataRole2](./Bilder/DataRole2.png)

Nun können Sie dort die Zugrifssberechtigungen auf die einzelnen Splaten jeder Tabelle und jedes Models festlegen.

![DataRole3](./Bilder/DataRole3.png)

Nun wechseln Sie auf den Reiter ***Mapped Enterprise Role or Group***. Dort kann die erstellte Data Role einer Applikationsuser Role oder einer ganzen Gruppe zugewiesen werden.

![DataRole4](./Bilder/DataRole4.png)

Geben Sie den Namen der Rolle ein und bestätigen Sie. In diesem Beispiel gilt die erstellte Data Role für alle User, die der Role appUserRole angehören.

![DataRole5](./Bilder/DataRole5.png)

Es können beliebig viele Data Roles erzeugt werden und auf die Rollen aufgeteilt werden. Zu beachten ist, dass eine Erlaubnis stärker gewichtet ist, als ein Verbot. Hat ein User keine positive Erlaubnis auf einer bestimmte Tabelle, hat er auch keinen Zugriff drauf. Hat ein User zwei Data Roles zugewiesen, wobei eine die Benutzung einer Spalte verbietet und die andere Data Role die Benutzung der Spalte erlaubt, hat der User im generellen eine Erlaubnis.

#6.2 Testen der VDB

Um die VDB zu testen, öffnen Sie die VDB und klicken Sie auf ***test***. Kommt es hier zu einem Fehler, dass ein Translator nicht eingestellt ist, oder die DataSource nicht angegeben ist, muss dem genannten Model per Rechtsklick -> Modeling die DataSource zugewiesen werden bzw. der Translator gesetzt werden.

![vdbtest1](./Bilder/vdbtest1.png)

Wenn auf ***test*** geklickt wird, wird die VDB auf dem Server deployed. Nun kann über das geöffnete Scrapbook die Verbindung getestet werden, indem SQL-Abfragen erstellt werden.

![vdbtest2](./Bilder/vdbtest2.png)

Links neben dem Scrapbook finden Sie die Verbindung zu der Datenbank. Dies läuft intern über den Teiid JDBC Treiber. Per Rechtsklick -> Properties -> Driver Properties auf der Verbindung können Sie die Anmeldedaten variieren. Hier können bspw. die Data Roles und die Zugriffsberechtigungen getestet werden.

## 7. Verbinden mit VDB

Die VDBs befinden sich nun auf dem Server und haben alle Metadaten um erfolgreiche Abfragen an die grundlegenden Datenquellen zu stellen. Von außen kann sich nun mit den VDBs verbunden werden.

![Schritt4](./Bilder/Schritt4.png)

### 7.1 Tableau

Um sich per Tableau mit einer VDB zu verbinden, benötigen wir einen ODBC-Treiber. Hierbei läuft auf dem Wildfly Server auf Port 35432 eine emulierte Schnittstelle zu einer PostreSQL-Datenbank, welcher wir nutzen werden. Dafür benötigen wir einen PostgreSQL-ODBC Treiber. Diese sind [hier](https://www.postgresql.org/ftp/odbc/versions/msi/)
 zu finden. Installieren Sie den neusten Treiber passend zu ihrem Betriebssystem. Unter Windows öffnen Sie das Programm zum Einrichten von ODBC-Datenquellen. Hier unbedingt auf das benutzte Betriebssystem achten, ob 32 oder 62-Bit und das demensprechende Programm öffnen.

![odbc1](./Bilder/odbc1.png)

Wählen Sie unter ***Systen-DSN*** ***PostgreSQL Unicode(64x)*** aus und klicken Sie Hinzufügen.

![odbc2](./Bilder/odbc2.png)

Geben Sie dort die Vebindungsdaten mit ihrem Server ein und den Port ***35432*** sowie die Logindaten.

![odbc3](./Bilder/odbc3.png)

Unter Datasource stellen Sie die gezeigten Einstellungen ein.

![odbc4](./Bilder/odbc4.png)



![odbc5](./Bilder/odbc5.png)

Damit ist einer Vebindung eingerichtet und testen Sie diese ggfs. mit dem Button ***test***.
Öffnen Sie nun Tableau und wählen Sie ***Andere Datenbanken (ODBC)*** aus.

![Tableau1](./Bilder/Tableau1.png)

Im geöffneten Fenster wählen Sie die erstellte DSN aus und klicken auf ***Verbinden*** und anschließend auf ***Anmelden***.

![Tableau2](./Bilder/Tableau2.png)

Sie sind nur per Tableau mit der VDB verbunden.
