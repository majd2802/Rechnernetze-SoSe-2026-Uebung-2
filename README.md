# TokenRingUDP

## Description

This Java project demonstrates the basic usage of the UDP protocol by establishing a
token ring network between multiple nodes. The ring is formed dynamically with a first
node serving as the ring leader. The leader is responsible for sending the first token
the moment a second node connects. Any subsequent node can join the ring by connecting
to the leader or any other node that is already part of the ring. A token is forwarded by a ring node
1 second after receiving it. Every time a node receives a token, it prints a message
to the console including the sequence number, the current size of the ring, and the IP
addresses and port number of all nodes in the ring.

## Usage

The implementation has been compiled against Java 21, although no special features are
used that would prevent it from running on older versions. The `Jackson` package is used
for JSON serialization and deserialization. To ease the process of running the program,
a *fatjar* has been created that includes all dependencies. The jar file `TokenRingUDP.jar`
has been added to the root of the repository.

---

# Übung 2 – Majd Abass

## Aufgabe 1: Wireshark-Schnitzeljagd

Ich habe die Datei `wshunt1_1.pcapng` in Wireshark geöffnet und mit verschiedenen Display-Filtern nach Zeichenfolgen gesucht, die aus genau acht Kleinbuchstaben und/oder Zahlen bestehen.

### Erste gültige Zeichenfolge

Zuerst habe ich HTTP-Pakete betrachtet.

Verwendeter Display-Filter:

```wireshark
http
```

Dabei wurde die Zeichenfolge gefunden:

```text
dzh20szq
```

Diese Zeichenfolge wurde auf der Webseite getestet:

```text
https://www.lanwan.ninja/dzh20szq
```

Die Webseite war gültig und zeigte den nächsten Hinweis:

```text
One HTTP error is different.
```

### Zweite gültige Zeichenfolge

Aufgrund des Hinweises habe ich nach HTTP-Fehlern gesucht.

Verwendeter Display-Filter:

```wireshark
http.response.code >= 400
```

Dabei fiel ein HTTP-Fehler besonders auf:

```text
HTTP/1.1 401 ra6rgv7h
```

Die gefundene Zeichenfolge war:

```text
ra6rgv7h
```

Zur genaueren Anzeige können auch folgende Filter verwendet werden:

```wireshark
frame.number == 493
```

```wireshark
frame contains "ra6rgv7h"
```

Die Zeichenfolge wurde auf der Webseite getestet:

```text
https://www.lanwan.ninja/ra6rgv7h
```

Die Webseite war gültig und führte zum nächsten Hinweis.

### Dritte und finale gültige Zeichenfolge

Danach habe ich ICMP-Pakete untersucht.

Verwendeter Display-Filter:

```wireshark
icmp
```

Dadurch wurden zwei ICMP-Pakete angezeigt. In Paket 342 befindet sich im Datenbereich die Zeichenfolge:

```text
nd7hark3
```

Zur genaueren Anzeige können auch folgende Filter verwendet werden:

```wireshark
frame.number == 342
```

```wireshark
icmp && frame contains "nd7hark3"
```

Die Zeichenfolge wurde auf der Webseite getestet:

```text
https://www.lanwan.ninja/nd7hark3
```

Die Webseite war gültig und bestätigte, dass die dritte und finale Herausforderung gelöst wurde.

### Zusammenfassung der gefundenen gültigen Zeichenfolgen

```text
dzh20szq
ra6rgv7h
nd7hark3
```

### Zusammenfassung der verwendeten Display-Filter

```wireshark
http
http.response.code >= 400
frame.number == 493
frame contains "ra6rgv7h"
icmp
frame.number == 342
icmp && frame contains "nd7hark3"
```
The ring leader can be started by just running `java -jar TokenRingUDP.jar`. The
leader then prints its IP address and port number to the console. Any subsequent
node can join by running `java -jar TokenRingUDP.jar <ip> <port>` for any `<ip>`
and `<port`> of a node already part of the ring.


## Aufgabe 2: Traceroute

### a) Erklärung der Funktionsweise von Traceroute

Traceroute zeigt die Zwischenstationen zwischen dem eigenen Rechner und einem Zielrechner an. Dafür werden Pakete mit unterschiedlichen TTL-Werten verschickt.

TTL bedeutet `Time To Live`. Der TTL-Wert gibt an, wie viele Router bzw. Hops ein Paket maximal passieren darf. Jeder Router auf dem Weg verringert den TTL-Wert um 1. Wenn der TTL-Wert bei einem Router 0 erreicht, wird das Paket verworfen. Der Router schickt dann normalerweise eine ICMP-Antwort zurück.

Traceroute nutzt dieses Verhalten aus:

- Zuerst wird ein Paket mit TTL = 1 gesendet. Dieses Paket erreicht nur den ersten Router.
- Danach wird ein Paket mit TTL = 2 gesendet. Dieses Paket erreicht den zweiten Router.
- Danach wird TTL = 3, TTL = 4 usw. verwendet.

So kann Traceroute Schritt für Schritt herausfinden, welche Router auf dem Weg zum Ziel liegen.

In der Ausgabe steht jede Zeile für einen Hop. Die drei Zeitwerte pro Zeile sind drei Messungen der Antwortzeit zu diesem Hop. Wenn ein Stern `*` angezeigt wird, kam für diese Messung keine Antwort zurück. Das kann durch Paketverlust, Firewalls oder Router-Konfigurationen passieren.

Als Beispiel habe ich `tracert 8.8.8.8` ausgeführt:

```text
Tracing route to dns.google [8.8.8.8]
over a maximum of 30 hops:

  1     3 ms     3 ms     4 ms  o2.box [192.168.1.1]
  2    15 ms    35 ms    15 ms  lo1.0006.acln.02.fra.de.net.telefonica.de [62.52.193.30]
  3    14 ms    14 ms    14 ms  lag38.0002.cord.02.fra.de.net.telefonica.de [62.53.12.110]
  4    14 ms    15 ms    16 ms  lag2.0001.corp.02.fra.de.net.telefonica.de [62.53.8.189]
  5    15 ms    29 ms    15 ms  72.14.213.80
  6    14 ms    14 ms    14 ms  142.251.65.69
  7   185 ms    17 ms    14 ms  172.253.66.137
  8    14 ms    14 ms    14 ms  dns.google [8.8.8.8]

Trace complete.
```

Man sieht hier, dass der erste Hop mein lokaler Router `o2.box` ist. Danach folgen mehrere Router im Netz von Telefónica in Frankfurt. Anschließend geht der Weg in Richtung Google. Das Ziel `dns.google [8.8.8.8]` wird nach 8 Hops erreicht.

### b) Traceroute zu `www.anu.edu.au`

Befehl:

```powershell
tracert www.anu.edu.au
```

Ausgabe:

```text
Tracing route to terra-web.anu.edu.au [130.56.67.33]
over a maximum of 30 hops:

  1     3 ms     3 ms     3 ms  o2.box [192.168.1.1]
  2    15 ms    15 ms    14 ms  lo1.0006.acln.02.fra.de.net.telefonica.de [62.52.193.30]
  3    14 ms    14 ms    14 ms  lag38.0002.cord.02.fra.de.net.telefonica.de [62.53.12.110]
  4    16 ms    14 ms    14 ms  lag2.0002.corp.02.fra.de.net.telefonica.de [62.53.9.53]
  5    24 ms    17 ms    14 ms  de-cix-ae10.mcs1.fra6.de.zip.zayo.com [80.81.192.255]
  6   150 ms   151 ms   150 ms  ae11.cr1.fra6.de.zip.zayo.com [64.125.20.124]
  7   150 ms   150 ms   150 ms  ae1.cr1.ams17.nl.zip.zayo.com [64.125.24.14]
  8   150 ms   150 ms   150 ms  ae0.cr1.ams10.nl.zip.zayo.com [64.125.23.184]
  9   150 ms   150 ms     *     ae10.cr1.man7.uk.eth.zayo.com [64.125.29.184]
 10   150 ms     *      150 ms  ae8.cr2.ewr14.us.zip.zayo.com [64.125.31.110]
 11     *        *        *     Request timed out.
 12     *        *        *     Request timed out.
 13     *      149 ms   150 ms  ae12.cr1.sea1.us.zip.zayo.com [64.125.19.4]
 14     *        *        *     Request timed out.
 15   150 ms   149 ms   150 ms  ae27.mpr1.sea1.us.zip.zayo.com [64.125.29.1]
 16   154 ms   150 ms   150 ms  64.125.193.130.i223.above.net [64.125.193.130]
 17   287 ms   287 ms   287 ms  et-10-0-5.170.pe1.brwy.nsw.aarnet.net.au [113.197.15.62]
 18   311 ms   290 ms   290 ms  et-0-3-0.pe1.actn.act.aarnet.net.au [113.197.15.11]
 19   290 ms   290 ms   290 ms  138.44.172.29
 20   291 ms   290 ms   290 ms  vlan-2100-palo.anu.edu.au [150.203.201.33]
 21   291 ms   291 ms   291 ms  policyforum.net [130.56.67.33]

Trace complete.
```

Bei `www.anu.edu.au` wird als Ziel `terra-web.anu.edu.au [130.56.67.33]` angezeigt. Der Weg ist deutlich länger als bei `8.8.8.8` und erreicht das Ziel erst nach 21 Hops.

Am Anfang geht der Weg über meinen lokalen Router und Telefónica in Frankfurt. Danach geht der Verkehr über Zayo, unter anderem über Frankfurt, Amsterdam, Manchester, Newark und Seattle. Ab Hop 17 sieht man `aarnet.net.au`. AARNet ist das australische Forschungs- und Bildungsnetz. Danach geht der Weg weiter in das Netz der Australian National University.

Auffällig ist, dass ab Hop 6 die Zeiten auf ungefähr 150 ms steigen. Später, ab dem australischen Teil der Route, steigen sie auf ungefähr 287 bis 311 ms. Das ist plausibel, weil Australien geographisch sehr weit von Deutschland entfernt ist.

Außerdem gibt es mehrere Stellen mit `*` oder `Request timed out`. Das bedeutet, dass einzelne Router nicht auf die Traceroute-Anfragen geantwortet haben. Trotzdem ist der Weg nicht abgebrochen, weil spätere Hops und schließlich das Ziel erreicht wurden.

### c) Traceroute zu `195.220.128.193`

Befehl:

```powershell
tracert 195.220.128.193
```

Ausgabe:

```text
Tracing route to proxy-web.u-paris.fr [195.220.128.193]
over a maximum of 30 hops:

  1     4 ms     3 ms     3 ms  o2.box [192.168.1.1]
  2    15 ms    15 ms    15 ms  lo1.0006.acln.02.fra.de.net.telefonica.de [62.52.193.30]
  3    15 ms    15 ms    14 ms  lag38.0001.cord.02.fra.de.net.telefonica.de [62.53.12.72]
  4    14 ms    14 ms    14 ms  lag0-0.0001.prrx.09.fra.de.net.telefonica.de [62.53.5.73]
  5    14 ms    14 ms    14 ms  as2603.frankfurt.megaport.com [62.69.146.103]
  6    25 ms    24 ms    24 ms  uk-hex.nordu.net [109.105.102.97]
  7    25 ms    25 ms    25 ms  ndn-gw.mx1.lon.uk.geant.net [109.105.102.98]
  8    31 ms    40 ms    31 ms  renater-ias-geant-gw.gen.ch.geant.net [83.97.89.13]
  9    40 ms    41 ms    41 ms  renater-ias-renater-gw.gen.ch.geant.net [83.97.89.14]
 10    42 ms    42 ms    44 ms  et-3-1-7-ren-nr-paris1-rtr-131.noc.renater.fr [193.51.180.166]
 11    43 ms    43 ms    42 ms  te0-0-0-12-ren-nr-odeon-rtr-091.noc.renater.fr [193.55.204.2]
 12    42 ms    42 ms    42 ms  xe-0-2-0-1-odeon-rtr-111-re0.noc.renater.fr [193.51.186.101]
 13  1063 ms    40 ms    39 ms  195.221.127.6
 14    42 ms    42 ms    42 ms  195.220.129.174
 15     *        *        *     Request timed out.
 16     *        *        *     Request timed out.
 17     *        *        *     Request timed out.
 18     *        *        *     Request timed out.
 19     *        *        *     Request timed out.
 20     *        *        *     Request timed out.
 21     *        *        *     Request timed out.
 22     *        *        *     Request timed out.
 23     *        *        *     Request timed out.
 24     *        *        *     Request timed out.
 25     *        *        *     Request timed out.
 26     *        *        *     Request timed out.
 27     *        *        *     Request timed out.
 28     *        *        *     Request timed out.
 29     *        *        *     Request timed out.
 30     *        *        *     Request timed out.

Trace complete.
```

Als Ziel wird `proxy-web.u-paris.fr [195.220.128.193]` angezeigt. Die Zieladresse gehört also zu `u-paris.fr`, also zur Universität Paris.

Der Weg verläuft zuerst über mein lokales Netzwerk:

```text
o2.box [192.168.1.1]
```

Danach geht der Weg über Telefónica in Frankfurt:

```text
fra.de.net.telefonica.de
```

Anschließend geht der Weg über Megaport in Frankfurt:

```text
as2603.frankfurt.megaport.com
```

Danach führt die Route über NORDUnet und GÉANT. GÉANT ist ein europäisches Forschungs- und Bildungsnetz. Danach sieht man RENATER:

```text
renater-ias-geant-gw.gen.ch.geant.net
renater-ias-renater-gw.gen.ch.geant.net
```

RENATER ist das französische Forschungs- und Bildungsnetz. Ab Hop 10 sieht man Router mit `paris1` und `odeon` im Namen:

```text
et-3-1-7-ren-nr-paris1-rtr-131.noc.renater.fr
te0-0-0-12-ren-nr-odeon-rtr-091.noc.renater.fr
xe-0-2-0-1-odeon-rtr-111-re0.noc.renater.fr
```

Das deutet darauf hin, dass der Weg nach Paris führt.

Geographische Einordnung:

- Start: eigener Rechner / lokaler Router
- Deutschland: Telefónica-Netz in Frankfurt
- Deutschland: Megaport Frankfurt
- Europa: NORDUnet / GÉANT
- Frankreich: RENATER
- Frankreich / Paris: RENATER-Router mit `paris1` und `odeon`
- Zielorganisation: Universität Paris / `u-paris.fr`

Ab Hop 15 erscheinen nur noch `Request timed out`. Trotzdem wurde vorher schon klar, dass die Route bis in das Netz von RENATER in Paris führt. Das Zielsystem oder spätere Router antworten wahrscheinlich nicht auf Traceroute-Anfragen. Das bedeutet nicht automatisch, dass der Server nicht erreichbar ist, sondern nur, dass keine Traceroute-Antwort zurückkommt.
