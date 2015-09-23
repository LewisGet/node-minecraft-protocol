# minecraft protocol
[![NPM version](https://img.shields.io/npm/v/minecraft-protocol.svg)](http://npmjs.com/package/minecraft-protocol)
[![Build Status](https://img.shields.io/circleci/project/PrismarineJS/node-minecraft-protocol/master.svg)](https://circleci.com/gh/PrismarineJS/node-minecraft-protocol)
[![Join the chat at https://gitter.im/PrismarineJS/node-minecraft-protocol](https://img.shields.io/badge/gitter-join%20chat-brightgreen.svg)](https://gitter.im/PrismarineJS/node-minecraft-protocol)

Parse and serialize minecraft packets, plus authentication and encryption.

## Features

 * Supports Minecraft version 1.8.8
 * Parses all packets and emits events with packet fields as JavaScript
   objects.
 * Send a packet by supplying fields as a JavaScript object.
 * Client
   - Authenticating and logging in
   - Encryption
   - Compression
   - Both online and offline mode
   - Respond to keep-alive packets.
   - Ping a server for status
 * Server
   - Online/Offline mode
   - Encryption
   - Compression
   - Handshake
   - Keep-alive checking
   - Ping status
 * Robust test coverage. See Test Coverage section below.
 * Optimized for rapidly staying up to date with Minecraft protocol updates.

## Projects Using node-minecraft-protocol

 * [mineflayer](https://github.com/andrewrk/mineflayer/) - create minecraft
   bots with a stable, high level API.
 * [mcserve](https://github.com/andrewrk/mcserve) - runs and monitors your
   minecraft server, provides real-time web interface, allow your users to
   create bots.
 * [flying-squid](https://github.com/mhsjlw/flying-squid) create minecraft
   servers with a high level API, also a minecraft server by itself.

## Usage

### Echo client example

```js
var mc = require('minecraft-protocol');
var client = mc.createClient({
  host: "localhost",   // optional
  port: 25565,         // optional
  username: "email@example.com",
  password: "12345678",
});
client.on('chat', function(packet) {
  // Listen for chat messages and echo them back.
  var jsonMsg = JSON.parse(packet.message);
  if (jsonMsg.translate == 'chat.type.announcement' || jsonMsg.translate == 'chat.type.text') {
    var username = jsonMsg.using[0];
    var msg = jsonMsg.using[1];
    if (username === client.username) return;
    client.write("chat", {
      message: msg
    });
  }
});
```

If the server is in offline mode, you may leave out the `password` option.

### Hello World server example

```js
var mc = require('minecraft-protocol');
var server = mc.createServer({
  'online-mode': true,   // optional
  encryption: true,      // optional
  host: '0.0.0.0',       // optional
  port: 25565,           // optional
});
server.on('login', function(client) {
  client.write('login', {
    entityId: client.id,
    levelType: 'default',
    gameMode: 0,
    dimension: 0,
    difficulty: 2,
    maxPlayers: server.maxPlayers
  });
  client.write('position', {
    x: 0,
    y: 1.62,
    z: 0,
    yaw: 0,
    pitch: 0,
    onGround: true
  });
  var msg = { translate: 'chat.type.announcement', using: [
    'Server',
    'Hello, ' + client.username
  ]};
  client.write("chat", { message: JSON.stringify(msg) });
});
```

## Installation

`npm install minecraft-protocol`

URSA, an optional dependency, should improve login times
for servers. However, it can be somewhat complicated to install.

Follow the instructions from
[Obvious/ursa](https://github.com/Obvious/ursa)

## Documentation

See [doc](doc/README.md)


## Testing

* Ensure your system has the `java` executable in `PATH`.
* Download the appropriate version of `minecraft_server.jar`.
* `MC_SERVER_JAR=path/to/minecraft_server.jar MC_USERNAME=email@example.com MC_PASSWORD=password npm test`

## Debugging

You can enable some protocol debugging output using `NODE_DEBUG` environment variable:

```bash
NODE_DEBUG="minecraft-protocol" node [...]
```

## History

See [history](HISTORY.md)
