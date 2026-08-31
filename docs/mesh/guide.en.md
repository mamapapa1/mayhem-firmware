# Meshtastic on the PortaPack: a guide

Languages: [Русский](guide.ru.md) | **English** | [Deutsch](guide.de.md) | [Español](guide.es.md)

The app lives under **Transceiver → Mesh**. Four tabs across the top: Chat, Nodes, Data,
Setup. Move between them with a finger or the navigation cross.

## Contents

- [Getting started](#getting-started)
- [Chat](#chat)
- [Conversations and channels](#conversations-and-channels)
- [Chat settings](#chat-settings)
- [Node list](#node-list)
- [Node card](#node-card)
- [Settings](#settings)

## Getting started

1. **Setup → Radio**, choose your region. Nothing works before this
2. **Setup → Profile**, set a name
3. Go back to **Chat** and wait: neighbours introduce themselves on their own, usually within
   a few minutes
4. Write to the shared channel with the `»` button

If nobody appears after a few minutes, check three things on the other side: the region, the
preset and the primary channel name. A difference in any one of them leaves both nodes deaf to
the other.

## Chat

![Empty chat](img/emptychat.jpg)

The top line shows the state of the radio and the link:

| field | meaning |
|---|---|
| `L:24V:26A:0` | receiver gain: LNA, VGA and the attenuator |
| `RX` | which way the radio is facing right now. `[RX]` or `[TX]` in square brackets means the direction is locked in the settings |
| `m:10` | how many messages are in the history |
| `-1/6` | signal level and quality of the last packet received |

The buttons along the bottom:

| button | what it does |
|---|---|
| `»` | opens the keyboard and sends what you type |
| `Ch:P` | the current channel. `P` is the primary one, a digit is one of yours |
| `>All` | who it goes to. Pressing it cycles through everyone, then each node you know |
| `All` | what to show: everything, or only the conversation |
| `X` | clear the screen |

The `RX`/`TX` field can be reached with the navigation cross, which highlights it in white,
and switched with the centre button. Three positions: send and receive, transmit only,
receive only.

### Reading a conversation

![Chat with messages](img/encryptedchat.jpg)

Your own messages sit on the right, everyone else's on the left. The time is in square
brackets. A `[DM]` mark means a direct message rather than the shared channel.

The coloured dot beside your own message is its delivery state:

- **yellow**: sent, nothing back yet
- **green**: received
- **red**: no acknowledgement came

For a message to everyone, green means a neighbour took it, sent it onward, and we heard our
own packet come back. That is the proof somebody is listening.

## Conversations and channels

![Conversation list](img/chatslist.jpg)

Opened with the `Ch:` button in the chat.

The top line is the primary channel: its name and its hash, `LongFast h08` for example. The
hash is the single byte Meshtastic puts in a packet instead of the channel name, computed from
the name and the key. Two nodes whose hashes differ cannot hear each other while both screens
look identical, which is why it is shown here.

The `DM [pkc] HeltecV4` line is a private conversation. `[pkc]` means that node's public key
is known and messages are encrypted for it alone.

Below are eight slots for your own channels. Pressing one does different things depending on
what is there:

- **an empty slot**: asks for a name, creates the channel and makes it current
- **the current channel**: asks for a passphrase, after which the channel is encrypted
- **another filled slot**: switches to it
- **the primary channel at the top**: goes back to the shared one

The buttons below: `Delete` removes the current channel, `Rnd key` sets a random key, `QR`
shows the channel as a code to carry to a phone, `Bell` sends an audible call.

**About interoperating.** The way a passphrase becomes a key here belongs to this app, not to
Meshtastic. Two PortaPacks with the same passphrase will understand each other. To talk to a
stock node on your own channel, enter that node's real key, 32 hexadecimal characters, instead
of a passphrase. The primary channel interoperates out of the box; it uses the well known key.

## Chat settings

![Chat settings](img/chatset.jpg)

| field | what it does |
|---|---|
| `Time` | the time format beside messages |
| `Names: colour` | tint node names with their own colour |
| `SD glyphs` | take a glyph table from the card so Cyrillic and other alphabets render |
| `Signal of last pkt` | show the signal level in the top line |
| `Beep on new msg`, `Bell` | sound on arrival |
| `Notify on S&F` | say when a packet was taken for later delivery |
| `Lines` | how many lines of scrollback to keep, about 40 bytes each |
| `Save msgs` | how many messages to keep on the card |
| `Resend` | how often to repeat an unacknowledged message, and how far apart |
| `Font` | glyph size |
| `Clear history` | erases channel and node conversations. Keys and the packet log stay |

## Node list

![Node list](img/nodelist.jpg)

Everyone heard. The columns are the last four digits of the node number, the name, the signal
level and the age. The stripe on the right is that node's colour, the same one its messages
carry in the chat.

The counter at the bottom shows how many nodes are held out of how many are possible, and how
many of those are currently reachable. Ten is the hard limit: the list lives in memory
permanently, and there is little of it in this device.

`Age v` flips the sort order, `Clear` empties the list, `Setup` opens its settings.

![List settings](img/nodesetup.jpg)

`Offline after` is how many minutes of silence before a node counts as gone. `Forget after` is
when to drop it entirely; zero means keep it for ever.

## Node card

Opened by pressing a row in the list. Nine pages, chosen with the field at the top.

### Identity

![Node identity](img/nodeidentity.jpg)

The number, the name, the short name, the board and the role. `Heard` gives both the last time
and the first.

`Key` is the node's public key. Until it arrives, a direct message is encrypted with the
channel key rather than for that node alone. The key travels only in a node's own
introduction, which is what the `Exchange info` button is for: it sends ours and asks for
theirs.

`Colour` picks the colour this node's messages are tinted with.

The buttons underneath work on all nine pages:

| button | what it does |
|---|---|
| `Message` | a private conversation with this node |
| `Exchange info` | swap introductions, which is how the key arrives |
| `Share QR` | show the node as a code to carry to a phone |
| `Map` | show it on the map, or read `No pos` if it has never sent one |
| `Trace` | ask which path reaches it; the answer lands in the chat |
| `Stats` | ask for the router's own counters |
| `Metrics` | ask for battery, voltage and airtime |

### Radio

![Node radio](img/noderadio.jpg)

Signal level and quality, how many hops away, battery, voltage, uptime, channel utilisation
and the share of airtime spent transmitting.

The signal level here is **not calibrated**. The HackRF's samples are eight bits wide, giving
about 42 dB of usable span, so the number is good for comparing nodes with each other but is
not real dBm. For a true figure, look at what the stock node on the other side reports.

### Counters

![Counters](img/nodestats.jpg)

Received, sent, bad, duplicated, relayed, nodes known, free memory, noise floor. They arrive
when you press `Stats`.

A stock Meshtastic node usually **does not answer** that request: those counters are meant for
a phone attached to the node. Between two PortaPacks it works.

The `Environ`, `Weather`, `Air qual`, `Power` and `Health` pages show sensor readings when a
node sends them. When it does not, the page says `not reported` rather than sitting blank.

## Settings

### Profile

![Profile](img/profileset.jpg)

The node name and short name everyone else will see. The node number can be typed or rolled
with the `rnd` button.

`Role` is how the node behaves in the mesh. `Client` suits almost everybody. The router roles
change how it passes other people's packets along, and are best left alone unless you mean it.

`Device` lets you present yourself as another board, if you would rather look like a familiar
node in somebody else's list.

### Radio

![Radio](img/radioset.jpg)

**Set your region first.** The frequency follows from it, and with the wrong region there is
no link at all while everything else still looks healthy.

| field | what it does |
|---|---|
| `Preset` | the modem mode. `LONG_FAST` is the default on every stock node |
| `Hop limit` | how many times a packet may be passed along |
| `CR` | coding rate, normally taken from the preset |
| `Freq` | frequency: from the region, or set by hand |
| `Freq slot` | which frequency slot within the region |
| `NodeInfo min` | how often to introduce yourself |
| `OK to MQTT` | whether gateways may publish our packets to the internet |
| `Ignore MQTT pkts` | hide packets that arrived through the internet |
| `TX pwr` | transmit power |

About `TX pwr`: it is the transmit gain, a number from 0 to 47, not a power. Neither `Region`
nor `Custom` is calibrated and nobody has measured what either comes to in watts, so they cannot
be used to stay inside a limit. Use the lowest setting that gets through, and do not transmit
without an antenna.

At the bottom, `Whip 1/4 wave` gives the quarter wave length for the current frequency. A wire
of that length on a metal base works noticeably better than a kit antenna cut for another band.

### Privacy

![Privacy](img/privacyset.jpg)

| field | what it does |
|---|---|
| `Send read receipts` | acknowledge that a message was read |
| `Encrypt DMs (PKC)` | encrypt direct messages for the recipient alone |
| `Randomize` | periodically change the details ticked below |
| `Announce NodeInfo now` | introduce yourself immediately |
| `No beacons or replies` | complete silence: say nothing, answer nothing |
| `Answer stats requests` | reply to requests for counters and metrics |

**Careful with silence.** With `No beacons or replies` on, the node never sends its
introduction, and the public key travels only in that introduction. Without the key nobody can
write to you privately, and you will not see why the messages fail.

### System

![System](img/systemset.jpg)

| field | what it does |
|---|---|
| `Log to SD card` | write a packet log to the card |
| `Store & Forward` | hold messages for nodes that cannot be heard right now |
| `buffer`, `hold` | how many messages to keep and for how many minutes |
| `Hold key repeats` | key repeat when a key is held down |
| `echo any` | echo any message that arrives |
| `text`, `signal`, `mem`, `uptime`, `build` | what to add to the echo |
| `Neighbors min` | how often to report neighbours |

Echo is the easy way to test range: one message from the far end checks both directions at
once. If the echo comes back, they heard you and you heard the answer.
