---
title: "Unifi AC"
---

Diese Anleitung ist für folgende Modelle:

* Unifi AC Lite  
* Unifi AC Mesh  
* Unifi AC Pro  



## Sicherstellen, dass Firmware mtd enthält
Konfiguriere deinen Computer auf ```192.168.1.100/24```. Verbinde dann mit dem Standardbenutzer ubnt (Passwort: ubnt) per SSH zum Router:
```
# ssh ubnt@192.168.1.20
# mtd
```
Hierbei kommt ggf. die Nachfrage, ob ein RSA-Hostkey gültig ist. Diese Frage ist in diesem Fall schlicht mit yes quittieren.

### Im Fehlerfall:
```
-ash: mtd: not found
```
Dann muss eine alte Stock-Firmware (Februar 2017) per TFTP-Client geflashed werden. Folge dazu Schritt 1.1. Gehe sonst zu Schritt 2.

## Firmware Downgrade per TFTP Client  
Dazu zunächst ältere Original-Firmware herunterladen: https://www.ubnt.com/download/unifi

Gerät auswählen
Firmware mit Stand aus Februar 2017 herunterladen
Dann das Gerät in die Recovery booten:

Gerät unter gedrückter RESET-Taste einschalten
Etwa 30 Sekunden RESET gedrückt halten, bis LED abwechselnd blau, weiß, aus blinkt.  

nicht: weiß und aus -- warten!  

sobald: blau und weiß -- upgrade läuft...  

Auf dem Computer die ```192.168.1.100/24``` konfigurieren  

Dann in den Download-Ordner wechseln und per TFTP die Firmware hochladen (Anleitung unten)
Warten bis Gerät neugestartet hat und dauerhaft weiß leuchtet.
```
# tftp 192.168.1.20
binary
rexmt 1
timeout 60
trace
put firmware.bin
```

## Freifunk-Firmware herunterladen
Unter https://firmware.karlsruhe.freifunk.net für das jeweilige Modell suchen und es herunterladen.

Für die Unifi AC Reihe gibt es kein ```factory``` Image, sondern nur das ```sysupgrade``` Image.

## Firmware hochladen
Eine weitere Konsole auf dem lokalen Computer öffnen, in den Download-Ordner wechseln und die Freifunk-Firmware per SCP hochladen.

```
# scp firmware.bin ubnt@192.168.1.20:/tmp
## Firmware flashen
```
In die Konsole mit der laufenden SSH-Verbindung mit dem Router wechseln und dort das Firmware-Image nach kernel0 und kernel1 schreiben:
```
# mtd write /tmp/firmware.bin kernel0
# mtd write /tmp/firmware.bin kernel1
```

## Bootselect schreiben
Nun die mtd Partition mit dem Label bs ausfindig machen und ein Nullbyte an den Anfang der Partition schreiben:  
```
# grep bs /proc/mtd
# dd if=/dev/zero bs=1 count=1 of=/dev/mtdX
```
In der Regel ist die gesuchte Partition ```/dev/mtd4```, das sollte aber durch den obigen Schritt sichergestellt werden.

## Neustarten
Danach den Router neustarten, sodass er die Freifunk-Firmware lädt.
```
# reboot
```
Während dem Bootvorgang blinkt das Gerät weiß, wenn es dauerhaft weiß leuchtet ist der Router fertig gestartet.

## Config Mode
Der Router ist nun im Config Mode, und unter ```192.168.1.1``` erreichbar. Er verteilt DHCP, damit dem Computer automatisch eine passende Adresse zugeteilt wird.

Starte nun die Konfiguration: http://192.168.1.1/

Wenn Schritt 8 nötig ist muss hier ein SSH Public-Key hinterlegt werden, siehe dazu Shell-Zugang#SSH-Keypair_generieren

## Für Outdoor fit machen (*nur* UAP-AC MESH)
Per SSH anmelden (Shell-Zugang#Verbindung_herstellen) und die folgenden Einstellungen kopieren.
```
# uci set gluon-core.@wireless[0].preserve_channels='1'
# uci commit gluon-core
# uci set wireless.radio0.channel='auto'
# uci set wireless.radio0.channels='100-140'
# uci set wireless.mesh_radio0.disabled='1'
# uci commit wireless
# wifi
```
Dadurch wird das Gerät für den Outdoorbetrieb eingerichtet:

Es werden eine Reihe von Outdoor-Kanälen konfiguriert
Auf diesen ist DFS erforderlich, was der Router somit beachtet
Zusätzlich wird das Mesh auf 5 GHz deaktiviert, da es nicht mit wechselnden Kanälen zurecht kommt
