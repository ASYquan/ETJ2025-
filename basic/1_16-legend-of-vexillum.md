# 1.16: Legend of Vexillum

## Description

A game where you progress through rooms to reach the final one containing the flag. The game connects to a server over the network.

## Solution

Started Wireshark before launching the game to capture everything from the start:

```bash
wireshark &
```

Played through the first couple of rooms normally while watching the traffic. The packets were plaintext TCP. Following the stream I could see the protocol immediately:

```
ROOM:1;ITEMS:none;COMMAND:enter;
ROOM:2;ITEMS:key;COMMAND:enter;
```

No encryption, no authentication, no session tokens. The server just trusts whatever the client sends.

Closed the game. Connected directly with netcat:

```bash
nc target 1234
```

Started sending crafted messages to skip straight to the final room. I needed to figure out what items were required, so I first sent a message claiming to be in the last room with no items and saw what the server responded with:

```
ROOM:final;ITEMS:none;COMMAND:enter;
# Server: You need the golden key.
```

So I needed the key. Grabbed it from room 2's response in the Wireshark capture, then sent:

```
ROOM:final;ITEMS:golden_key;COMMAND:enter;
```

Server responded with the flag. Never touched the game UI after the initial recon.

Flag: `dcee2dbef8ad3dc077ba21dacafb9a97`

When a game communicates over plaintext TCP, the game itself is just a pretty frontend. The real logic is on the server and it'll trust anything you send it.
