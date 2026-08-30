# Meshtastic on HackRF + PortaPack

[Русский](README.md) · **English** · [Deutsch](README.de.md) · [Español](README.es.md)

This is a fork of the [Mayhem](https://github.com/portapack-mayhem/mayhem-firmware) firmware
for the HackRF with a PortaPack. It differs from the original in one thing: a **Meshtastic**
app has been added, and a built firmware is published under Releases so you can try it without
building anything.

The work is proposed upstream as
[PR #3306](https://github.com/portapack-mayhem/mayhem-firmware/pull/3306). Until that is
accepted, this fork lives on its own.

## Read this before you press transmit

**Mayhem can transmit, and that puts obligations on you.** A HackRF with a PortaPack is not a
receiver with a dongle, it is a full transmitter from 1 MHz to 6 GHz. In most countries
transmitting outside the amateur and licence-free bands is prohibited, and inside them it is
limited by power and by duty cycle. Responsibility for what you radiate, and where, is yours
and not the firmware's. The Mayhem project says as much, and nothing changes here.

**Safe transmit power has not been measured.** There is a `TX pwr` field going up to `Max`, but
nobody has checked what actual power that corresponds to, whether it stays inside the limits
where you are, or how the amplifier takes a mismatched antenna. Treat it as unverified.
Sensible caution:

- use the lowest power that gets through, not the highest available
- do not transmit without an antenna, or with one cut for another band; reflected power goes
  back into the amplifier
- do not hold a continuous transmission; the 868 MHz band in Europe has duty cycle limits
- if you are unsure whether the band is open where you are, stay on receive: the app has an
  `RX only` mode for exactly this

The app does not bypass or weaken any limit: it speaks the same protocol on the same
frequencies as stock Meshtastic nodes.

**User guide with screenshots:**
[Русский](docs/mesh/guide.ru.md) ·
[English](docs/mesh/guide.en.md) ·
[Deutsch](docs/mesh/guide.de.md) ·
[Español](docs/mesh/guide.es.md)

## What is interesting here

The HackRF has nothing in it that understands LoRa. It hands over a stream of samples and takes
one back. So the whole physical layer is written from scratch and runs on the second core:
preamble detection, time and frequency synchronisation, dechirping against a reference, the
Fourier transform, Gray coding, interleaving, Hamming, whitening. In both directions, inside
54 kB of buffers, against a 1.024 ms deadline per buffer.

Put plainly, it is a software LoRa transceiver that holds a link with stock Meshtastic nodes:
it exchanges messages, encrypts channels, keeps a node list, receives telemetry and positions,
draws a map, and passes other people's packets along.

Range so far: **2.6 km line of sight** on LongFast, with plenty of signal margin left, on an
antenna from the HackRF kit that is not cut for the band. The limit has not been found.

## Quick start

1. Download `portapack-mayhem_OCI.ppfw.tar.gz` from
   [Releases](https://github.com/mamapapa1/mayhem-firmware/releases)
2. Extract it **to the root of the SD card**. It holds three folders and you need all three:

   | folder | what is in it |
   |---|---|
   | `FIRMWARE` | the firmware |
   | `APPS` | external applications |
   | `BASEBAND` | signal processing images |

3. Card into the device, **Utilities → Flash Utility**, pick `portapack-mayhem_dev.bin`
4. After flashing, **power the device off completely** rather than restarting it
5. **Transceiver → Mesh**, then **Setup → Radio** and choose your region

Without a region there is no link at all, and everything else will look healthy. That is the
first thing to check when nothing arrives.

`BASEBAND` is not decoration. The app does not fit in flash whole, so eleven images were moved
to the card. Extract only `FIRMWARE` and eleven other apps will report `NoImg` and refuse to
start.

## What works

Seven of the nine modem presets, in both directions. Confirmed on the air against a stock
Heltec V4: messages, encrypted channels, telemetry, positions, the clock taken from a
neighbour, traceroute, and the requests the Meshtastic phone app makes of a node.

Details, including the preset table, are in the [guide](docs/mesh/guide.en.md).

## What does not work

An honest list, so you do not go looking for the fault in your own hands:

- **SF12 receive** (`LONG_SLOW`). Transmit works, receive does not: the algorithm does not fit
  the time budget on this hardware
- **Weather and SubGhzD do not start** and report `NoImg`. Their images are larger than the
  memory left by the time you have navigated to them
- **Passphrases for your own channels** work between PortaPacks only. To talk to a stock node
  on your own channel, enter that node's real key, 32 characters, instead of a passphrase
- **The signal level is not calibrated.** Good for comparing nodes with each other, but it is
  not real dBm

## Warning

This is **not an official Mayhem release** and not a Meshtastic release. It is built from a
branch that has not been accepted upstream and changes as the review goes on.

Not everything works, and something may break. The device does sometimes stop with an error
screen; power cycling fixes it. Your settings and messages on the card survive.

Memory in the device is scarce and is not returned until it restarts. In practice: open several
apps in a row and the next one may report `NoImg`. Power it off and on.

Transmitting is subject to the rules where you are. The 868 and 915 MHz bands are not open
everywhere, nor at any power.

Flashing is reversible: the official firmware can always be put back the same way.

## Tell me what went wrong

This is the most useful thing you can do. I have one PortaPack, one Heltec and one city. Your
surroundings will almost certainly turn up something I cannot see.

Please open an [issue](https://github.com/mamapapa1/mayhem-firmware/issues). Most useful:

- what you did and what happened instead
- the PortaPack model and the preset
- if the device stopped, **photograph the whole screen**: the reason is written on it
- if the trouble is with the link: what the other side shows, and what it is
- the build version, visible on the bottom line of the main menu

Of particular interest: range where you live, nodes I do not have (T-Beam, RAK, T-Deck), and
anything to do with Cyrillic and other alphabets in the chat.

## Building from source

```
docker run --rm -v "$(pwd):/havoc" portapack-dev:latest make -j4
```

Checks that need no hardware:

```
cd tools/lora_bench && make
```

## Credits and disclaimers

The whole base belongs to the [Mayhem](https://github.com/portapack-mayhem/mayhem-firmware)
project and its contributors. One app has been added here.

Uses the Meshtastic protocol. Not affiliated with or endorsed by Meshtastic LLC.

Licence is inherited from Mayhem: GPL-2.0-or-later.
