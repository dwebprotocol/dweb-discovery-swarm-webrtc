# dweb-discovery-swarm-webrtc


> dweb-discovery-swarm for webrtc

<p align="center">
  <img src="https://user-images.githubusercontent.com/819446/64871056-d6a2d480-d61a-11e9-9d93-b79a5f0e822a.gif" alt="force-graph">
</p>

This module provides a "similar" API to discovery-swarm but for WebRTC connections.

It has a few differences to discovery-swarm:

- It needs a signaling server. We give you [one](#server).
- It uses [mmst](https://github.com/RangerMauve/mostly-minimal-spanning-tree) to minimize the connections. Check the example.
- `join` and `leave` only accepts Buffers.
- `leave` and `close` accepts a callback argument or returns a Promise.

## Install

```
$ npm install dweb-discovery-swarm-webrtc
```

## Usage

### <a name="server"></a>Server

You can run your own signal server by running:

```
$ dweb-discovery-swarm-webrtc --port=4000
```

#### Public Servers

- wss://signal-v3.dwebx.net
- wss://signal-v3.dsocial.network

#### Deploy to Heroku

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/dwebprotocol/dweb-discovery-swarm-webrtc/tree/master)

### Client

```javascript
const crypto = require('crypto')
const swarm = require('dweb-discovery-swarm-webrtc')

const sw = swarm({
  bootstrap: ['ws://localhost:4000']
})

const topic = crypto.createHash('sha256')
  .update('my-discovery-swarm-topic')
  .digest()

sw.join(topic)

sw.on('connection', peer => {
  // connected
})
```

## API

#### `const sw = swarm(opts)`

Creates a new Swarm.

`opts` include:

```javascript
{
  id: crypto.randomBytes(32), // peer-id for user
  bootstrap: [string], // urls to your websocket endpoints
  stream: (info) => stream, // stream to replicate across peers
  simplePeer: {}, // options for the simplePeer instances,
  maxPeers: 5, // max connections by peer
  timeout: 15 * 1000, // defines the time to wait to establish a connection
}
```

#### `sw.join(Buffer)`

Join a specific channel. We use behind it `simple-signal` + `simple-peer`.

#### `const promise = sw.leave(Buffer)`

Leave from specific channel. Destroy all the connections and leave the channel.

#### `const promise = sw.close([callback])`

Close the entire swarm. Destroy all the connections and disconnect from the signal.

#### `const arrayOfPeers = sw.getPeers([channel])`

Returns the list of connected peers for a specific channel.

Channel is `optional`, if you don't pass it you get the entire list of peers.

#### `sw.connect(channel: Buffer, peerId: Buffer) -> Promise<SimplePeer>`

Connect directly to a specific peer.

### Events

#### `sw.on('handshaking', function(connection, info) { ... })`

Emitted when you've connected to a peer and are now initializing the connection's session. Info is an object that contains information about the connection.

`info` include:

``` js
{
  id: Buffer // the remote peer's peer-id.
  channel: Buffer // the channel this connection was initiated on.
  initiator: Boolean // whether we initiated the connection or someone else did
}
```

#### `sw.on('connection', function(connection, info) { ... })`

Emitted when you have fully connected to another peer. Info is an object that contains info about the connection.

#### `sw.on('connection-closed', function(connection, info) { ... })`

Emitted when you've disconnected from a peer. Info is an object that contains info about the connection.

#### `sw.on('leave', function(channel) { ... })`

Emitted when you left a channel.

#### `sw.on('close', function() { ... })`

Emitted when the swarm was closed.

#### `sw.on('candidates-updated', function(channel, candidates) { ... })`

Emitted when the candidates peer for a specific channel was updated. `candidates` is an array of Buffer id.

## <a name="issues"></a> Issues

:bug: If you found an issue we encourage you to report it on [github](https://github.com/dwebprotocol/dweb-discovery-swarm-webrtc/issues). Please specify your OS and the actions to reproduce it.

## <a name="contribute"></a> Contributing

:busts_in_silhouette: Ideas and contributions to the project are welcome. You must follow this [guideline](https://github.com/dwebprotocol/dweb-discovery-swarm-webrtc/blob/master/CONTRIBUTING.md).

## License

MIT © A [**DWEB**](http://dwebx.org/) project
