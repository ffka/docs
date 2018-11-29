---
title: "Mesh-VPN"
---

Mit dem Mesh-VPN ist eine Verbindung zu unseren Gateway-Servern möglich ohne das dafür eine Richtfunk Verbindung benötigt wird.

Als Software kommt dafür [FastD](https://projects.universe-factory.net/projects/fastd/wiki) zum Einsatz.


Auf das VPN wird direkt auf allen Gateway Servern betrieben. Dabei stellen diese jeweils 2 Prozesse zur verfügung, einen auschließlich für Verbindungen über IPv4 einen zweiten auschließlich für IPv6. Pro Prozess stehen 50 Slots für Verbindungen offen.

Als Methoden stehen "null+salsa2012+umac, null+salsa2012+gmac, salsa2012+umac, salsa2012+gmac" zur verfügung. In unserer Firmware ist "salsa2012+umac" standartmäißg ausgewählt.


**Blacklist & Keys**

Jede Mesh-VPN verbindung benörtigt einen eindeutigen Krytografischen Key.

Neue Keys werden von den VPN-Servern automatisch akzeptiert.

Falls das Mesh-VPN missbraucht wird kann der Zugang zum Mesh-VPN gesperrt werden.
Vorher wird jedoch in jedem Fall die betroffene Person/ der betroffene Knoten Betreiber kontaktiert.

Wir pflegen eine Liste der gesperrten Keys öffentlich in unserm GIT.

Falls versucht wird die Keysperre zu umgehen, werden wir einzelne IP-Adressen von unserem Mesh-VPN ausschließen.
