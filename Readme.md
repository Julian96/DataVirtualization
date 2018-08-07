# Dokumentation Teiid Daten Virtualisierung
___
## Einleitung
In dieser Dokumentation wird die Installation der Teiid Daten Virtualisierung beschrieben, sowie das Aufsetzen des Servers und das Verbinden mit diversen Datenquellen. Zudem werden generelle Grundlagen der Verwendung des Wildfly Servers und Verwendung der JBoss Developer Studio Oberfläche geklärt. Der Ordner mit benötigten Dateien ist [hier](https://infocloud.infomotion.de/index.php/apps/files/?dir=/Teiid%20Dokumentation&fileid=4750825) zu finden.
## Verwendete Technologien
- Rechner 1(Server)
	- OS:Red Hat Enterprise Linux
	- Applikationsserver: Wildfly
	- Datenvirtualisierung: Teiid
- Rechner 2(User)
	- Red Hat JBoss Developer Studio
	- Putty

## Grundlagen
Dieses Kapitel beschäftigt sich mit der verwendeten Begriffsterminologie und dem groben Aufbau der verwendeten Infrastruktur. Für die Datenvirtualisierung wird ein zwei geteiltes System verwendet. Auf der einen Seite befindet sich ein Server (VM oder ein physischer Rechner) und auf der anderen Seite der User mit seiner lokalen Maschine. In diesem Beispiel läuft eine VM auf einem anderen Rechner und wird werden mit [Putty](https://www.putty.org/) auf den Server zugreifen. Auf dem Server läuft das Betriebssystem [Red Hat Enterprise Linux](https://www.redhat.com/de/technologies/linux-platforms/enterprise-linux). Hierfür wird ein Account bei Red Hat benötigt. Auf diser Maschine wird ein Applikationsserver für die Datenvirtualisierung installiert. Hier gibt es zwei Möglichkeiten:

- [JBoss Enterprise Application Plattform](https://developers.redhat.com/products/eap/overview/)  (JBoss EAP)
- [Wildfly](http://www.wildfly.org/)  

Bei EAP handelt es sich um die offizielle Version von Red Hat und bei Wildfly um die Community Version. EAP basiert auf den Komponenten von Wildfly.  
Zusätzlich muss eine Datenvirtualisierung auf diesem Applikationsserver installiert werden. Auch hierbei gibt es wieder zwei Möglichkeiten:

- [JBoss Data Virtualization](https://developers.redhat.com/products/datavirt/overview/)
- [Teiid](http://teiid.io/)

Auch hier ist JBoss Data Virtualization die angebotene Version von Red Hat und Teiid die Community Version. JBoss Data Virtualization wird von Red Hat in Zukunft nicht weiter entwickelt und supported (Stand: 07.08.2018).
Aus diesem Grund wird im Laufe der Dokumentation ein Wildfly Server mit installiertem Teiid verwendet.
![BildÜbersicht](./Bilder/Übersichts.png)
Um nun
## Server aufstezen
a
