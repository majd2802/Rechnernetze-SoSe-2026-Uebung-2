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


