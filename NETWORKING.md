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

**Table 1.3.0.1**

Parameter | Argument | Notes
--- | --- | ---
Maximum Message Length | 2 * (10 ** 6) | A message must not exceed 2MB in size

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

### 1.3.1 – Message Flags

Following message flags are available and supported:

Flag | Status | Since Protocol | Purpose
--- | --- | --- | ---
MSG_TYPE_HANDSHAKE | active | v0.1.0 | Exchange handshake strings
MSG_TYPE_NOTIFICATION | active | v0.1.0 | Notification/acknowledgment message
MSG_TYPE_REQ_PEERS | active | v0.1.0 | Request a list of peers
MSG_TYPE_RES_PEERS | active | v0.1.0 | Response having a serialized list of peers

#### 1.3.1.2 – Notifications/Acknowledgments

Flag | Status | Since Protocol | Purpose
--- | --- | --- | ---
NOTE_TYPE_HS_ACCEPT | active | v0.1.0 | Acknowledgment for a handshake string being accepted by remote peer
NOTE_TYPE_HS_REJECT | active | v0.1.0 | Acknowledgment for a handshake string being rejected by remote peer
NOTE_TYPE_VIOLATION | active | v0.1.0 | Notification from a remote peer indicating a violation was detected from this node

### 1.3.3 – Violations

Communication rules are STRICTLY ENFORCED, and if a peer is misbehaving, its connection is to be dropped almost immediately with a increased violation count. 
If a pre-configured `N` number of violations are reached, a remote peer's IP address is added to blacklist for a period of time.

**Table 1.3.3.1**

Violation | Code | Hex | Description
--- | --- | --- | ---
INVALID_MESSAGE_ENCODING | 1701 | 0x06a5 | Received message is not a valid hexadecimal string. All messages must adhere defined data domain.
META_MSG_SIZE_UNDERFLOW | 1702 | 0x06a6 | Underflow exception. Message received is less then minimum expected 4 bytes
META_MSG_SIZE_OVERFLOW | 1703 | 0x06a7 | Overflow exception. Message received exceeds hard limit (i.e. `n`MB + 4 bytes)
BAD_MSG_TYPE_FLAG | 1704 | 0x06a8 | Invalid or disabled message flag was specified in first 2 bytes of message
MSG_LEN_UNDERFLOW | 1705 | 0x06a9 | Message len (after first 4 bytes) is less then mentioned number of bytes
MSG_LEN_OVERFLOW | 1706 | 0x06aa | Message len (after first 4 bytes) exceeds mentioned number of bytes
NO_HANDSHAKE_CALL | 1707 | 0x06ab | Sent a message without going through proper handshake channel first
BAD_HANDSHAKE_STR | 1708 | 0x06ac | Bad handshake string was sent, a notification message of type `NOTE_TYPE_HS_REJECT` may also be sent to peer with this
MSG_ARGS_REQUIRED | 1709 | 0x06ad | No arguments; This usually indicates that more bytes were expected after initial 4 but none were received
BAD_ARGUMENTS | 1710 | 0x06ae | Bad arguments; This usually indicated that message/arguments sent (after initial 4 bytes of message) are either bad or incorrectly serialized
UNNECESSARY_MSG_RECEIVED | 1711 | 0x06af | Remote peer has sent an unnecessary message

#### 1.3.3.2 – Rules

* Upon violation a notification message `MSG_TYPE_NOTIFICATION` with flag `NOTE_TYPE_VIOLATION` followed by appropriate violation code 
 is sent remote peer and connection is immediately dropped.
 * Upon `N` violations, IP address of remote peer is added to blacklist for `X` hours.
 * For pruning of these violations, 1 violation is removed/decreased for an IP address per `Y` hours.
 
 **Table 1.3.3.2.1**
 
 Item | Variable | Suggested Value
 --- | --- | ---
 Maximum violations | N | 10
 Blacklist period | X | 3 Hours
 Prune 1 violation | Y | Every 1 hour

