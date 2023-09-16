# iob.vis.vitocal250
Visualisierung Kältekreislauf der Viessmann Wärmepumpe VitoCal 250 in ioBroker vis

## "Übersicht Kältekreislauf" als Visualisierung in ioBroker
Ich habe den **Diagnosebildschirm der Wärempumpe** von der VitoCal abfotografiert und für ioBroker aufbereitet und erweitert. Zusätzlich wird der Betrieb des Heizstabs und die elektrischen und thermischen Leistungen angezeigt. Vielen Dank an Fa. Viessmann für die gelungene Darstellung des Kältekreislaufs!

Nach erfolgreicher Installation kann man die Arbeit der Wärmepumpe als ioBroker-Visualisierung live beobachten.

## Voraussetzungen
Um die benötigten Daten aus der VitoCal auszulesen, ist mir nur ein Weg bekannt: USB2CAN-Adapter an den externen CAN-Bus der VitoCal anschließen und per Python-Skript die benötigten Daten abfragen. Über das API-Interface von Viessmann kommt man nicht an (alle) diese Daten ran.

Foglendes wird benötigt:
- Linux-System mit Python 3, MQTT-CLient und ioBroker. Z.B. auf einem Rapsberry Pi.
- USB2CAN-Adapter
- Installation von [open3e](https://github.com/abnoname/open3e)

## Einrichten der Visualisierung
- Zunächst muss dafür gesorgt werden, dass die benötigten Daten regelmäßig per MQTT versendet werden. Das erledigt open3e mit folgendem Aufruf: `python3 Open3Eclient.py -c can0 -dev vcal -m localhost:1883:open3e/vitocal -mfstr {didNumber:04d}_{didName} -t 30 -r 268,269,271,274,282,284,318,320,321,322,324,325,355,381,389,391,396,531,902,1043,1339,1607,1643,1769,1770,1771,1772,1775,1776,1815,1816,1817,2320,2333,2334,2346,2351,2352,2369,2370,2487,2488,2495,2496,2735,2744,2746,3016`
- Damit werden die Daten alle 30 Sekunden per MQTT an den lokalen Broker gesendet. Z.B. die Temperatur am Eingang des Kompressors wird dann mit folgender Message vesendet: `open3e/vitocal/0321_CompressorInletTemperatureSensor`
- Für die Visualisierung in vis müssen die Dateien unter `img/` nach `/vis.0/main/img/` hochgeladen werden und der Inhalt von `view/Kaeltekreis.json` als View importiert werden.
- Ggf. die json-Struktur vor dem Import editieren (Suchen/Ersetzen), falls ein anderer MQTT-Broker als `mqtt-client.0` oder eine andere vis-Instanz verwendet wird.

## Läuft
Ein Screenshot der Visualisierung ist unter screeshot/ zu sehen.

Die Darstellung passt sich an, abhängig davon, ob der Kompressor in Betrieb ist oder nicht. Das gilt auch für den Heizstab.
