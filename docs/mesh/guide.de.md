# Meshtastic auf dem PortaPack: Anleitung

Sprachen: [Русский](guide.ru.md) | [English](guide.en.md) | **Deutsch** | [Español](guide.es.md)

Die App liegt unter **Transceiver → Mesh**. Oben vier Reiter: Chat, Nodes, Data, Setup.
Zwischen ihnen wechselt man mit dem Finger oder dem Steuerkreuz.

## Inhalt

- [Erste Schritte](#erste-schritte)
- [Chat](#chat)
- [Unterhaltungen und Kanäle](#unterhaltungen-und-kanäle)
- [Chat-Einstellungen](#chat-einstellungen)
- [Knotenliste](#knotenliste)
- [Knotenkarte](#knotenkarte)
- [Einstellungen](#einstellungen)

## Erste Schritte

1. **Setup → Radio**, Region wählen. Vorher geht nichts
2. **Setup → Profile**, einen Namen setzen
3. Zurück in den **Chat** und warten: Nachbarn stellen sich von selbst vor, meist binnen
   weniger Minuten
4. Mit `»` in den gemeinsamen Kanal schreiben

Bleibt es nach einigen Minuten still, prüfen Sie auf der Gegenseite drei Dinge: Region, Preset
und den Namen des Hauptkanals. Weicht eines davon ab, sind beide Knoten füreinander taub.

## Chat

![Leerer Chat](img/emptychat.jpg)

Die oberste Zeile zeigt den Zustand von Funkteil und Verbindung:

| Feld | Bedeutung |
|---|---|
| `L:24V:26A:0` | Empfangsverstärkung: LNA, VGA und Dämpfungsglied |
| `RX` | die aktuelle Richtung. `[RX]` oder `[TX]` in eckigen Klammern heißt, dass die Richtung in den Einstellungen festgelegt ist |
| `m:10` | wie viele Nachrichten im Verlauf liegen |
| `-1/6` | Pegel und Güte des zuletzt empfangenen Pakets |

Die Schaltflächen unten:

| Schaltfläche | Wirkung |
|---|---|
| `»` | öffnet die Tastatur und sendet das Getippte |
| `Ch:P` | der aktuelle Kanal. `P` ist der Hauptkanal, eine Ziffer einer der eigenen |
| `>All` | an wen es geht. Tippen schaltet weiter: an alle, dann jeder bekannte Knoten |
| `All` | was gezeigt wird: alles oder nur die Unterhaltung |
| `X` | Bildschirm leeren |

Das Feld `RX`/`TX` lässt sich mit dem Steuerkreuz anwählen, dann wird es weiß hinterlegt, und
mit der mittleren Taste umschalten. Drei Stellungen: senden und empfangen, nur senden, nur
empfangen.

### Eine Unterhaltung lesen

![Chat mit Nachrichten](img/encryptedchat.jpg)

Eigene Nachrichten stehen rechts, fremde links. Die Uhrzeit steht in eckigen Klammern. Die
Marke `[DM]` bedeutet eine Direktnachricht statt des gemeinsamen Kanals.

Der farbige Punkt neben der eigenen Nachricht zeigt die Zustellung:

- **gelb**: gesendet, noch keine Antwort
- **grün**: angekommen
- **rot**: keine Bestätigung erhalten

Bei einer Nachricht an alle heißt grün, dass ein Nachbar sie angenommen und weitergegeben hat
und wir das eigene Paket zurückgehört haben. Das ist der Beweis, dass jemand zuhört.

## Unterhaltungen und Kanäle

![Liste der Unterhaltungen](img/chatslist.jpg)

Wird im Chat mit `Ch:` geöffnet.

Die oberste Zeile ist der Hauptkanal: Name und Prüfbyte, etwa `LongFast h08`. Das Prüfbyte ist
das eine Byte, das Meshtastic anstelle des Kanalnamens ins Paket schreibt, berechnet aus Name
und Schlüssel. Zwei Knoten mit verschiedenen Prüfbytes hören einander nicht, obwohl beide
Bildschirme gleich aussehen. Deshalb steht es hier.

Die Zeile `DM [pkc] HeltecV4` ist eine private Unterhaltung. `[pkc]` heißt, dass der
öffentliche Schlüssel dieses Knotens bekannt ist und nur für ihn verschlüsselt wird.

Darunter acht Plätze für eigene Kanäle. Ein Tippen bewirkt je nach Zustand:

- **leerer Platz**: fragt nach einem Namen, legt den Kanal an und macht ihn zum aktuellen
- **aktueller Kanal**: fragt nach einem Kennwort, danach ist der Kanal verschlüsselt
- **anderer belegter Platz**: schaltet dorthin um
- **Hauptkanal oben**: zurück zum gemeinsamen Kanal

Die Schaltflächen unten: `Delete` löscht den aktuellen Kanal, `Rnd key` setzt einen zufälligen
Schlüssel, `QR` zeigt den Kanal als Code fürs Telefon, `Bell` sendet einen hörbaren Ruf.

**Zur Verträglichkeit.** Wie aus einem Kennwort ein Schlüssel wird, ist hier Sache dieser App
und nicht von Meshtastic. Zwei PortaPacks mit demselben Kennwort verstehen einander. Um mit
einem Seriengerät auf einem eigenen Kanal zu sprechen, geben Sie statt des Kennworts dessen
echten Schlüssel ein, 32 Zeichen hexadezimal. Der Hauptkanal ist sofort verträglich, dort liegt
der allgemein bekannte Schlüssel.

## Chat-Einstellungen

![Chat-Einstellungen](img/chatset.jpg)

| Feld | Wirkung |
|---|---|
| `Time` | Zeitformat neben den Nachrichten |
| `Names: colour` | Knotennamen in ihrer eigenen Farbe einfärben |
| `SD glyphs` | eine Zeichentabelle von der Karte laden, damit Kyrillisch und andere Alphabete erscheinen |
| `Signal of last pkt` | den Pegel in der Kopfzeile anzeigen |
| `Beep on new msg`, `Bell` | Ton bei Empfang |
| `Notify on S&F` | melden, wenn ein Paket zur späteren Zustellung angenommen wurde |
| `Lines` | wie viele Zeilen Verlauf gehalten werden, etwa 40 Byte je Zeile |
| `Save msgs` | wie viele Nachrichten auf der Karte bleiben |
| `Resend` | wie oft eine unbestätigte Nachricht wiederholt wird und in welchem Abstand |
| `Font` | Zeichengröße |
| `Clear history` | löscht die Unterhaltungen. Schlüssel und Paketprotokoll bleiben |

## Knotenliste

![Knotenliste](img/nodelist.jpg)

Alle, die gehört wurden. Die Spalten: die letzten vier Stellen der Knotennummer, der Name, der
Pegel und das Alter. Der Streifen rechts ist die Farbe des Knotens, dieselbe wie im Chat.

Der Zähler unten sagt, wie viele Knoten von wie vielen möglichen gespeichert sind und wie viele
davon gerade erreichbar sind. Zehn ist die harte Grenze: die Liste liegt dauerhaft im Speicher,
und davon hat dieses Gerät wenig.

`Age v` dreht die Sortierung um, `Clear` leert die Liste, `Setup` öffnet ihre Einstellungen.

![Listeneinstellungen](img/nodesetup.jpg)

`Offline after` ist die Zahl der Minuten Schweigen, nach denen ein Knoten als fort gilt.
`Forget after` sagt, wann er ganz verschwindet; Null heißt für immer behalten.

## Knotenkarte

Öffnet sich beim Tippen auf eine Zeile der Liste. Neun Seiten, gewählt über das Feld oben.

### Identität

![Identität](img/nodeidentity.jpg)

Nummer, Name, Kurzname, Platine und Rolle. `Heard` nennt den letzten und den ersten Empfang.

`Key` ist der öffentliche Schlüssel des Knotens. Solange er fehlt, wird eine Direktnachricht
mit dem Kanalschlüssel verschlüsselt und nicht für diesen Knoten allein. Der Schlüssel reist
nur in der Selbstvorstellung eines Knotens, und genau dafür ist `Exchange info` da: es sendet
unsere und bittet um die fremde.

`Colour` wählt die Farbe, in der die Nachrichten dieses Knotens erscheinen.

Die Schaltflächen darunter gelten auf allen neun Seiten:

| Schaltfläche | Wirkung |
|---|---|
| `Message` | private Unterhaltung mit diesem Knoten |
| `Exchange info` | Vorstellungen tauschen, so kommt der Schlüssel |
| `Share QR` | den Knoten als Code fürs Telefon zeigen |
| `Map` | auf der Karte zeigen, oder `No pos`, wenn nie eine Position kam |
| `Trace` | den Weg dorthin erfragen; die Antwort erscheint im Chat |
| `Stats` | die Zähler des Routers erfragen |
| `Metrics` | Akku, Spannung und Sendezeit erfragen |

### Funk

![Funk](img/noderadio.jpg)

Pegel und Güte, Zahl der Zwischenstationen, Akku, Spannung, Laufzeit, Kanalauslastung und
Anteil der Sendezeit.

Der Pegel ist hier **nicht geeicht**. Die Abtastwerte des HackRF sind acht Bit breit, der
nutzbare Bereich beträgt etwa 42 dB. Die Zahl taugt zum Vergleich der Knoten untereinander,
aber nicht als echtes dBm. Den richtigen Wert zeigt das Seriengerät auf der Gegenseite.

### Zähler

![Zähler](img/nodestats.jpg)

Empfangen, gesendet, fehlerhaft, doppelt, weitergegeben, bekannte Knoten, freier Speicher,
Rauschpegel. Sie kommen auf `Stats`.

Ein Meshtastic-Seriengerät **antwortet darauf meist nicht**: diese Zähler sind für ein am
Knoten angeschlossenes Telefon gedacht. Zwischen zwei PortaPacks funktioniert es.

Die Seiten `Environ`, `Weather`, `Air qual`, `Power` und `Health` zeigen Messwerte, sofern ein
Knoten sie sendet. Sonst steht dort `not reported`, statt dass die Seite leer bleibt.

## Einstellungen

### Profil

![Profil](img/profileset.jpg)

Name und Kurzname, die alle anderen sehen. Die Knotennummer lässt sich eintippen oder mit `rnd`
neu würfeln.

`Role` ist die Rolle im Netz. `Client` passt fast immer. Die Router-Rollen ändern, wie fremde
Pakete weitergereicht werden, und sollten ohne Grund unangetastet bleiben.

`Device` lässt Sie sich als eine andere Platine ausgeben, falls Sie in fremden Listen wie ein
gewohnter Knoten aussehen möchten.

### Funk

![Funk](img/radioset.jpg)

**Zuerst die Region wählen.** Daraus folgt die Frequenz, und mit der falschen Region gibt es
gar keine Verbindung, während alles andere gesund aussieht.

| Feld | Wirkung |
|---|---|
| `Preset` | die Modulationsart. `LONG_FAST` ist auf jedem Seriengerät voreingestellt |
| `Hop limit` | wie oft ein Paket weitergereicht werden darf |
| `CR` | Kodierrate, üblicherweise aus dem Preset |
| `Freq` | Frequenz: aus der Region oder von Hand |
| `Freq slot` | welcher Frequenzplatz innerhalb der Region |
| `NodeInfo min` | wie oft man sich vorstellt |
| `OK to MQTT` | ob Übergänge unsere Pakete ins Internet stellen dürfen |
| `Ignore MQTT pkts` | Pakete aus dem Internet ausblenden |
| `TX pwr` | Sendeleistung |

**Wir wissen nicht, mit welcher Leistung wir senden.** Dieses Feld stellt keine Leistung ein,
sondern die Sendeverstärkung des HackRF, eine Zahl von 0 bis 47. `Custom` ist dieselbe
Verstärkung von Hand, `Region` senkt sie gegenüber dem Höchstwert um den Unterschied zwischen
dem Grenzwert Ihrer Region und dem großzügigsten bekannten. Keine Stellung ist geeicht, welcher
tatsächlichen Leistung sie entspricht, hat niemand gemessen, und einen Grenzwert kann man damit
nicht sicher einhalten. Nehmen Sie die kleinste Stufe, mit der die Verbindung steht, und senden
Sie nicht ohne Antenne.

Unten steht `Whip 1/4 wave` mit der Viertelwellenlänge für die eingestellte Frequenz. Ein Draht
dieser Länge auf einer Metallfläche arbeitet spürbar besser als eine Beipack-Antenne für ein
anderes Band.

### Privatsphäre

![Privatsphäre](img/privacyset.jpg)

| Feld | Wirkung |
|---|---|
| `Send read receipts` | bestätigen, dass eine Nachricht gelesen wurde |
| `Encrypt DMs (PKC)` | Direktnachrichten nur für den Empfänger verschlüsseln |
| `Randomize` | die unten angehakten Angaben regelmäßig ändern |
| `Announce NodeInfo now` | sich sofort vorstellen |
| `No beacons or replies` | vollständiges Schweigen: nichts sagen, nichts beantworten |
| `Answer stats requests` | Anfragen nach Zählern und Messwerten beantworten |

**Vorsicht beim Schweigen.** Mit `No beacons or replies` sendet der Knoten seine Vorstellung
nie, und der öffentliche Schlüssel reist ausschließlich darin. Ohne den Schlüssel kann Ihnen
niemand privat schreiben, und Sie werden nicht sehen, woran es liegt.

### System

![System](img/systemset.jpg)

| Feld | Wirkung |
|---|---|
| `Log to SD card` | ein Paketprotokoll auf der Karte führen |
| `Store & Forward` | Nachrichten für gerade nicht hörbare Knoten zurückhalten |
| `buffer`, `hold` | wie viele Nachrichten und wie viele Minuten |
| `Hold key repeats` | Tastenwiederholung beim Halten |
| `echo any` | jede eingehende Nachricht als Echo zurücksenden |
| `text`, `signal`, `mem`, `uptime`, `build` | was dem Echo beigefügt wird |
| `Neighbors min` | wie oft Nachbarn gemeldet werden |

Das Echo ist der bequemste Reichweitentest: eine Nachricht von der Gegenseite prüft beide
Richtungen auf einmal. Kommt das Echo zurück, hat man Sie gehört und Sie die Antwort.
