# IkaGo

This is the development documentation of IkaGo.

## Terms and Adjustments

`Link Layer`: Ethernet and loopback layer.

`Network Layer`: IPv4 and ARP layer.

`Transport Layer`: TCP, UDP and ICMPv4 layer.

## Packet Capturing

### Between Client and Server (FakeTCP)

TCP and fragments packets received with the same source's address of the other's will be captured.

### Between Sources and Client

TCP, UDP, ICMPv4 and fragments packets received with the same source's address of `-r` will be captured.

TCP, UDP, ICMPv4 and fragments packets received with the same address of server will be ignored.

### Between Server and Destinations

TCP, UDP, ICMPv4 and fragments packets will be captured.

TCP, UDP, ICMPv4 and fragments packets received with the same port of server's listen port will be ignored.

## Connection

Clients and server establish a FakeTCP connection at the beginning of transmission. All transmissions will use this connection.

At the beginning of establishing the connection, the TCP 3-way handshaking is simulated. And the 3rd handshaking of ACK is the only packet with empty payload during the whole process of transmission.

Either client or server sends packet starts with IPv4 ID `0` and TCP sequence `0`.

Neither client nor server replies ACK passively.

## Transmission

### Between Client and Server (FakeTCP)

All packets transmitted must contain exactly a link layer, a network layer and a transport layer.

**Transmission between clients and server must be in IPv4.**

**Packets transmitted between clients and server will be reassembled**, but the fragmentation information will be kept and restored in server and clients.

Packets transmitted between clients and server will not be verified.

Transmission size information displayed in verbose log in the client is the size of application layer in reassembled packets from the server.

Transmission size information displayed in verbose log in the server is the size of application layer in reassembled packets from the client.

#### Packet Structure

<p align="center">
  <img src="/assets/packet.jpg" alt="diagram">
</p>

### Between Sources and Client, Server and Destinations

All packets transmitted must contain exactly a link layer, a network layer and a transport layer.

**Transmission between sources and clients, server and destinations must be in IPv4.**

IPv4 options will not be processed.

Transmission size information displayed in verbose log in the client is the size of network, transport and application layer in packets from sources.

Transmission size information displayed in verbose log in the server is the size of network, transport and application layer in packets from destinations.

## Encryption

IkaGo supports authenticated encryption.

If encryption is enabled, the wrapped packets will be composed of one-time nonce, data and hash.

The size of hash is always 16 Bytes, and the size of nonce depends on the method of encryption.

### Nonce Size

| Method      | Size (Bytes) |
| ----------- | :---: |
| AES-128-GCM | 12 |
| AES-192-GCM | 12 |
| AES-256-GCM | 12 |
| ChaCha20-Poly1305 | 12 |
| XChaCha20-Poly1305 | 24 |
