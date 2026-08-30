# Meshtastic auf HackRF + PortaPack

[Русский](README.md) · [English](README.en.md) · **Deutsch** · [Español](README.es.md)

Dies ist ein Fork der Firmware [Mayhem](https://github.com/portapack-mayhem/mayhem-firmware)
für den HackRF mit PortaPack. Vom Original unterscheidet er sich in einem Punkt: hier ist eine
**Meshtastic**-App hinzugekommen, und eine fertige Firmware liegt unter Releases, damit man sie
ohne eigenes Bauen ausprobieren kann.

Die Arbeit ist dem Hauptprojekt als
[PR #3306](https://github.com/portapack-mayhem/mayhem-firmware/pull/3306) vorgeschlagen. Solange
der nicht angenommen ist, lebt dieser Fork für sich.

## Lesen Sie dies, bevor Sie senden

**Mayhem kann senden, und das legt Ihnen Pflichten auf.** Ein HackRF mit PortaPack ist kein
Empfänger mit Stick, sondern ein vollwertiger Sender von 1 MHz bis 6 GHz. In den meisten
Ländern ist das Senden außerhalb der Amateur- und der lizenzfreien Bänder verboten, und
innerhalb davon ist es nach Leistung und Sendezeitanteil begrenzt. Die Verantwortung dafür, was
und auf welcher Frequenz Sie abstrahlen, liegt bei Ihnen und nicht bei der Firmware. Das
Mayhem-Projekt sagt das ebenso deutlich, und daran ändert sich hier nichts.

**Die sichere Sendeleistung ist nicht gemessen.** Es gibt ein Feld `TX pwr` bis `Max`, aber
niemand hat geprüft, welcher tatsächlichen Leistung das entspricht, ob sie die Grenzwerte bei
Ihnen einhält und wie der Verstärker eine fehlangepasste Antenne verträgt. Betrachten Sie es
als ungeprüft. Vernünftige Vorsicht:

- die kleinste Leistung nehmen, mit der die Verbindung steht, nicht die größte verfügbare
- nicht ohne Antenne senden und nicht mit einer für ein anderes Band; reflektierte Leistung
  läuft in den Verstärker zurück
- keine Dauersendung; im 868-MHz-Band gelten in Europa Grenzen für den Sendezeitanteil
- wenn Sie nicht sicher sind, ob das Band bei Ihnen frei ist, bleiben Sie beim Empfang: die App
  hat dafür die Betriebsart `RX only`

Die App umgeht und schwächt keine Vorschrift: sie spricht dasselbe Protokoll auf denselben
Frequenzen wie Meshtastic-Seriengeräte.

**Bedienungsanleitung mit Bildschirmfotos:**
[Русский](docs/mesh/guide.ru.md) ·
[English](docs/mesh/guide.en.md) ·
[Deutsch](docs/mesh/guide.de.md) ·
[Español](docs/mesh/guide.es.md)

## Was hier interessant ist

Im HackRF steckt nichts, was LoRa verstünde. Er liefert einen Strom von Abtastwerten und nimmt
ebensolchen zurück. Deshalb ist die gesamte Bitübertragungsschicht neu geschrieben und läuft
auf dem zweiten Kern: Präambelsuche, Zeit- und Frequenzsynchronisation, Entzerrung gegen einen
Referenz-Chirp, Fourier-Transformation, Gray-Kodierung, Verschachtelung, Hamming, Weißung. In
beide Richtungen, in 54 kB Puffern, bei 1,024 ms Frist je Puffer.

Kurz gesagt: ein softwaredefinierter LoRa-Transceiver, der eine Verbindung zu
Meshtastic-Seriengeräten hält. Er schreibt Nachrichten, verschlüsselt Kanäle, führt eine
Knotenliste, empfängt Telemetrie und Positionen, zeigt eine Karte und gibt fremde Pakete weiter.

Reichweite bisher: **2,6 km Sichtverbindung** auf LongFast, mit reichlich Signalreserve, und das
an einer Antenne aus dem HackRF-Zubehör, die für dieses Band nicht geschnitten ist. Die Grenze
ist nicht gefunden.

## Schnellstart

1. `portapack-mayhem_OCI.ppfw.tar.gz` aus den
   [Releases](https://github.com/mamapapa1/mayhem-firmware/releases) laden
2. **In das Wurzelverzeichnis der Speicherkarte** entpacken. Darin liegen drei Ordner, und alle
   drei werden gebraucht:

   | Ordner | Inhalt |
   |---|---|
   | `FIRMWARE` | die Firmware |
   | `APPS` | externe Anwendungen |
   | `BASEBAND` | Signalverarbeitungs-Abbilder |

3. Karte ins Gerät, **Utilities → Flash Utility**, `portapack-mayhem_dev.bin` wählen
4. Nach dem Flashen das Gerät **vollständig ausschalten**, nicht nur neu starten
5. **Transceiver → Mesh**, dann **Setup → Radio** und die Region wählen

Ohne Region gibt es überhaupt keine Verbindung, während alles andere gesund aussieht. Das ist
das Erste, was man prüft, wenn nichts ankommt.

Der Ordner `BASEBAND` ist keine Zierde. Die App passt nicht vollständig in den Flash, deshalb
sind elf Abbilder auf die Karte ausgelagert. Entpacken Sie nur `FIRMWARE`, dann melden elf
fremde Anwendungen `NoImg` und starten nicht.

## Was funktioniert

Sieben der neun Modemarten, in beide Richtungen. In der Luft gegen ein Heltec V4 mit
Serienfirmware geprüft: Nachrichten, verschlüsselte Kanäle, Telemetrie, Positionen, die Uhr vom
Nachbarn, Routenverfolgung und die Anfragen, die die Meshtastic-Telefon-App an einen Knoten
stellt.

Einzelheiten samt Tabelle der Modemarten stehen in der [Anleitung](docs/mesh/guide.de.md).

## Was nicht funktioniert

Eine ehrliche Liste, damit Sie den Fehler nicht bei sich suchen:

- **Empfang auf SF12** (`LONG_SLOW`). Senden geht, Empfangen nicht: der Algorithmus passt auf
  dieser Hardware nicht in die verfügbare Zeit
- **Weather und SubGhzD starten nicht** und melden `NoImg`. Ihre Abbilder sind größer als der
  Speicher, der übrig ist, wenn man sich zu ihnen durchgeklickt hat
- **Kennwörter eigener Kanäle** vertragen sich nur zwischen PortaPacks. Für ein Seriengerät
  geben Sie statt des Kennworts dessen echten Schlüssel ein, 32 Zeichen
- **Der Pegel ist nicht geeicht.** Gut zum Vergleich der Knoten untereinander, aber kein echtes
  dBm

## Warnung

Dies ist **keine offizielle Mayhem-Ausgabe** und keine Meshtastic-Ausgabe. Sie stammt aus einem
Zweig, der noch nicht übernommen ist und sich im Lauf der Durchsicht ändert.

Nicht alles funktioniert, und etwas kann kaputtgehen. Es kommt vor, dass das Gerät mit einem
Fehlerbildschirm stehen bleibt; Aus- und Einschalten hilft. Einstellungen und Nachrichten auf
der Karte bleiben erhalten.

Der Speicher im Gerät ist knapp und kommt bis zum Neustart nicht zurück. In der Praxis: öffnet
man mehrere Anwendungen nacheinander, meldet die nächste womöglich `NoImg`. Gerät aus und wieder
ein.

Das Senden unterliegt den Vorschriften Ihres Landes. Die Bänder 868 und 915 MHz sind nicht
überall und nicht bei jeder Leistung frei.

Das Flashen ist umkehrbar: die offizielle Firmware lässt sich jederzeit auf demselben Weg
zurückspielen.

## Sagen Sie, was bei Ihnen nicht ging

Das ist das Nützlichste, was Sie tun können. Ich habe ein PortaPack, ein Heltec und eine Stadt.
Ihre Umgebung fördert fast sicher etwas zutage, das ich nicht sehen kann.

Bitte eröffnen Sie ein [Issue](https://github.com/mamapapa1/mayhem-firmware/issues). Am
nützlichsten:

- was Sie getan haben und was stattdessen geschah
- Modell des PortaPack und die Modemart
- wenn das Gerät stehen blieb, **fotografieren Sie den ganzen Bildschirm**: der Grund steht
  darauf
- bei Verbindungsproblemen: was die Gegenseite anzeigt und welches Gerät das ist
- die Version der Sicherung, sichtbar in der untersten Zeile des Hauptmenüs

Besonders interessieren: die Reichweite bei Ihnen, Knoten, die ich nicht habe (T-Beam, RAK,
T-Deck), und alles rund um Kyrillisch und andere Alphabete im Chat.

## Aus den Quellen bauen

```
docker run --rm -v "$(pwd):/havoc" portapack-dev:latest make -j4
```

Prüfungen ohne Hardware:

```
cd tools/lora_bench && make
```

## Dank und Hinweise

Die gesamte Grundlage gehört dem Projekt
[Mayhem](https://github.com/portapack-mayhem/mayhem-firmware) und seinen Mitwirkenden. Hier ist
eine App hinzugefügt.

Verwendet das Meshtastic-Protokoll. Nicht mit Meshtastic LLC verbunden und von dort nicht
freigegeben.

Die Lizenz stammt von Mayhem: GPL-2.0-or-later.
