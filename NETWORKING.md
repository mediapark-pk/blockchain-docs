# 1 — Networking

Primary method of communication between peers is based on TCP/IP sockets. 

**Table 1.0.1**  

Parameter | Argument | Notes
--- | --- | ---
Domain | AF_INET | IPv4 Internet based protocols (TCP and UDP)
Type | SOCK_STREAM | Provides sequenced, reliable, full-duplex, connection-based byte streams. An out-of-band data transmission mechanism may be supported. The TCP protocol is based on this socket type. 
Protocol | SOL_TCP | TCP/IP

## 1.1 — Peers Soft/Hard-cap

* A hard-cap is enforced maximum number of active TCP socket connections to maintain.
* Depending on hardware specification, a node can configure a soft-cap of TCP socket connections to maintain which is any case MUST NOT exceed mentioned hard-cap.

**Table 1.1.1**

Parameter | Argument
--- | ---
Hard cap | 50
Soft cap | *1-50*

## 1.2 — Listening / P2P Server

* It is optional for a node to establish a server for incoming connections.
* Implementing applications should make this a configuration variable.
* If disabled, a node will still be able to establish connections to other nodes and communicate normally.
* A node may create a listening server on a port of its own choosing, however suggested default port is recommended to create P2P server.

**Table 1.2.1**

Parameter | Argument
--- | ---
Default P2P Port | 18301
Backlog | 50

## 1.3 — Messages

### 1.3.1 – Serialization

* All messages must be sent in hexadecimal and may be delimited by `\n` (unix line feed character).
* First 2 bytes of a message is MESSAGE_FLAG which identifies the type of message, in base16 little endian form.
* Next 2 bytes of a message is the total length of message following these first 4 bytes, in base16 little endian form.

**Table 1.3.1.1**

Parameter | Argument
--- | ---
Example message | 2f4d656469615061726b2e504b2f
Message Length | 14 bytes

**Table 1.3.1.2**

Index | Parameter | Type | Notes | Example Value
--- | --- | --- | --- | ---
1 | Flag | Uint16LE | MSG_TYPE_NOTIFICATION *(dec: `1100`)* | 0x4c04
2 | Message length | Uint16LE | 14 bytes length of an example message from [Table 1.3.1.1](#) | 0x0e00
3 | Actual message | Base16 | An example message from [Table 1.3.1.1](#) | 0x2f4d656469615061726b2e504b2f

Thus, this message can be sent over P2P network as:
`4c040e002f4d656469615061726b2e504b2f`

### 1.3.3 – Violations

Communication rules are STRICTLY ENFORCED, and if a peer is misbehaving, its connection is to be dropped almost immediately with a increased violation count. 
If a pre-configured `N` number of violations are reached, a remote peer's IP address is added to blacklist for a period of time.

**Table 1.3.1.1**

Violation | Code | Hex | Description
--- | --- | --- | ---
INVALID_MESSAGE_ENCODING | 1701 | 0x06a5 | Received message is not a valid hexadecimal string. All messages must 

## Message Formats



### Handshake Message



Param | Example Value | Notes
--- | --- | ---
Network passphrase | MediaPark-Blockchain-Prototype:10400 | Blockchain network identifier, name followed by an integer (i.e. year)
Protocol version | 10 | Implemented protocol version as unsigned integer
Listen port | 18301 | Port # where this node is listening for connections; 0 if not listening

**Example Handshake:**  
`/MediaPark-Blockchain-Prototype:2020/10/18301/`

This entire string is to be converted to hexadecimal representation (based on ASCII table chars 32-128)

#### Serialization

Make a concat string


