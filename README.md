# ft_irc

[![en](https://img.shields.io/badge/lang-en-pink.svg)](README.md)
[![fr](https://img.shields.io/badge/lang-fr-purple.svg)](README.fr.md)


An IRC server written in C++, built to comply with RFC 2812. It handles multiple simultaneous clients, manages channels and user permissions, and implements the core IRC command set — including the full MODE system. A ChatGPT-powered bot, developed by [Théo Zeribi](https://github.com/TheoZerbibi), is also integrated as a conversational agent accessible directly from any IRC channel.

This is a 42 school project, completed as a two-person team.

---

## What this project covers

IRC is a text-based protocol from the early internet era, still widely used in developer communities. Building a compliant server from scratch requires handling low-level networking, stateful client sessions, and a precisely specified command protocol.

This implementation covers:

- **Socket management and client lifecycle** — accepting connections, maintaining per-client read buffers, handling partial and multi-command input, registration flow with timeout
- **Full IRC registration sequence** — PASS / NICK / USER handshake, numeric reply system (RPL/ERR) per RFC 2812
- **Channel system** — creation, join with optional key, topic, user limits, invite-only mode
- **MODE implementation** — operator flag (`o`), invite-only (`i`), channel key (`k`), user limit (`l`), topic restriction (`t`)
- **Operator commands** — KICK, INVITE, TOPIC, MODE
- **Signal handling** — graceful behavior on CTRL+C and CTRL+D
- **ChatGPT bot** — a separate IRC client that connects to the server and responds to messages using the OpenAI API (by Théo Zeribi)

---

## Architecture

The server runs a single-threaded event loop using `select()`, handling all connected file descriptors without blocking. Each client has its own command buffer to handle TCP stream fragmentation. Commands are each implemented as a dedicated class, making the command set easy to extend.

```
ft_irc/
├── inc/
│   ├── Irc.hpp              # Server core
│   ├── User.hpp
│   ├── Channel.hpp
│   ├── Reply.hpp            # Numeric replies (RPL/ERR)
│   └── Commands/            # One class per IRC command
│       ├── Command.hpp
│       ├── JoinCommand.hpp
│       ├── ModeCommand.hpp
│       ├── PrivMsgCommand.hpp
│       └── ...
├── srcs/                    # Mirrors inc/ structure
├── bot/                     # Standalone ChatGPT bot (Théo Zeribi)
│   └── srcs/
│       ├── Bot.cpp          # OpenAI API integration
│       └── Socket.cpp
├── test/                    # Test scripts and proxy tool
│   ├── ncirc.sh
│   ├── proxy/               # TCP proxy for traffic inspection
│   └── Input/               # Raw IRC command sequences
└── Makefile
```

---

## Stack

- **Language**: C++98
- **Networking**: POSIX sockets (`sys/socket.h`, `netinet/in.h`, `arpa/inet.h`)
- **Multiplexing**: `select()`
- **Tested with**: irssi

> Note: the server was developed and tested against irssi. Behavior with other clients may vary.

---

## Usage

```bash
make
./ircserv <port> <password>
```

Connect with any IRC client:

```bash
# irssi example
irssi
/connect localhost <port> <password>
```

## External Ressource

- [Network programming guide](https://beej.us/guide/bgnet/pdf/bgnet_a4_c_1.pdf)

- [Irc : Protocol explanation](http://chi.cs.uchicago.edu/chirc/irc.html)

- [ModernIrc : Complete command reference](https://modern.ircdocs.horse/)

- [IRC2812](https://www.tech-invite.com/y25/tinv-ietf-rfc-2812.html) 

- [Irssi Doc](https://irssi.org/New-users/)

- [Numerical Replies](https://github.com/marineks/Ft_irc/blob/main/includes/Numerical_replies.hpp)
