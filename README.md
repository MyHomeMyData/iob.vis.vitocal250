# iob.vis.vitocal250
Visualisierung des Kältekreislaufs der Viessmann Wärmepumpe VitoCal 250 in ioBroker vis

![image](https://github.com/MyHomeMyData/iob.vis.vitocal250/assets/144950531/ae8db68a-1c3b-445d-9f34-360ff7229547)

## "Übersicht Kältekreislauf" als Visualisierung in ioBroker
Ich habe den **Diagnosebildschirm der Wärempumpe** von der VitoCal abfotografiert, für ioBroker aufbereitet und erweitert. Zusätzlich werden der Betrieb des Heizstabs und die elektrischen und thermischen Leistungen angezeigt, sowie die Temperaturen der Pufferspeicher für Heizkreis 1 und Warmwasser. Vielen Dank an Fa. Viessmann für die gelungene Darstellung des Kältekreislaufs!

Nach erfolgreicher Installation kann man die Arbeit der Wärmepumpe als ioBroker-Visualisierung live beobachten.

## Voraussetzungen
Um die benötigten Daten aus der VitoCal auszulesen, ist mir nur ein Weg bekannt: USB2CAN-Adapter an den externen CAN-Bus der VitoCal anschließen und mit dem Adapter e3oncan oder per Python-Skript die benötigten Daten abfragen. Über das API-Interface von Viessmann kommt man nicht an (alle) diese Daten ran.

Foglendes wird benötigt:
- Linux-System mit ioBroker, z.B. auf einem Rapsberry Pi.
- Bei Verwendung von open3e zusätzlich: Python 3 und MQTT-CLient
- Installation des Adapters [e3oncan](https://github.com/MyHomeMyData/ioBroker.e3oncan) oder von [open3e](https://github.com/abnoname/open3e)
- USB2CAN-Adapter

## Einrichten der Visualisierung mit ioBroker-Adapter e3oncan
- Der Adapter e3oncan ist im Beta-Repository von ioBroker verfügbar. Der Adapter benötige **keine** Installation von open3e.
- Um die benötigten Daten regelmäßig abzufragen, richtet man in der Adapterkonfiguration bei "Zuweisungen zum externen CAN-Adapter" eine Abfrage für das Gerät "HPMUMASTER" für folgende Datenpunkte ein: `268,269,271,274,284,318,320,321,322,324,325,355,381,389,391,396,1043,1769,1771,1772,1775,1776,2333,2334,2346,2351,2487,2488,2496,2735,3016`. Die Wiederholungszeit kann z.B. auf 30 Sekunden festgelegt werden.
- Für die Visualisierung in vis müssen die Dateien aus diesem Repository unter `img/` nach `/vis.0/main/img/` hochgeladen werden, dazu verwendet man am besten den Dateimanager im Vis Editor.
- Nun erzeugt man eine View und importiert den Inhalt von `view/Kaeltekreis_e3oncan.json` per "copy & paste".
- Ggf. die json-Struktur aus `view/Kaeltekreis_e3oncan.json` vor dem Importieren editieren (Suchen/Ersetzen), falls eine andere vis-Instanz verwendet wird.

## Einrichten der Visualisierung mit open3e
- Zunächst muss dafür gesorgt werden, dass die benötigten Daten regelmäßig per MQTT versendet werden. Das erledigt open3e mit folgendem Aufruf: `python3 Open3Eclient.py -c can0 -dev vcal -m localhost:1883:open3e/vitocal -mfstr {didNumber:04d}_{didName} -t 30 -r 268,269,271,274,284,318,320,321,322,324,325,355,381,389,391,396,1043,1769,1771,1772,1775,1776,2333,2334,2346,2351,2487,2488,2496,2735,3016`
- Damit werden die Daten alle 30 Sekunden per MQTT an den lokalen Broker gesendet. Z.B. die Temperatur am Eingang des Kompressors wird dann mit folgender Message vesendet: `open3e/vitocal/0321_CompressorInletTemperatureSensor`
- Für die Visualisierung in vis müssen die Dateien aus diesem Repository unter `img/` nach `/vis.0/main/img/` hochgeladen werden, dazu verwendet man am besten den Dateimanager im Vis Editor.
- Nun erzeugt man eine View und importiert den Inhalt von `view/Kaeltekreis.json` per "copy & paste".
- Ggf. die json-Struktur aus `view/Kaeltekreis.json` vor dem Importieren editieren (Suchen/Ersetzen), falls ein anderer MQTT-Broker als `mqtt-client.0` oder eine andere vis-Instanz verwendet wird.
- Abhängig von der verwendeten Version von open3e muss noch eine Änderung an den Objekt-Eigenschaften von "2351_HeatPumpCompressor" vorgenommen werden: Format -> Number, Step -> 1. Siehe Issue#1.

## Läuft
Die Darstellung passt sich an, abhängig davon, ob der Kompressor in Betrieb ist oder nicht. Auch die Betriebsart (Heizen oder Warmwasserbereitung) ist direkt erkennbar.
