# 1 -- Handshake

Handshake is the very first operation that will be performed by the application. Handshake is done via passing a message between two peers who just connected and whether they will hold this connection or will be dropped depends on the handshake message.

-**Sender Handshake Message**

When a new peer is connected the peer sends the handshake string. Default handshake message is sent . Message String consists of three parts.

**1.1 -- Passphrase**

String is encoded with base 16 encoding. In the table it can be seen how a passphrase message is converted into base 16

**Table 1.1.0**
| Passphrase                                | Passphrase in base-16                                        |
| ----------------------------------------- | ------------------------------------------------------------ |
| **/MediaPark-Blockchain-Prototype:2020/** | 2f4d656469615061726b2d426c6f636b636861696e2d50726f746f747970653a323032302f |

**1.2 -- Passphrase Length**

passphrase message length is calculated and converted into Hexadecimal number as shown below in **Table 1.2**

**Table 1.2.0**

| Passphrase Length | Passphrase Length In HexaDecimal |
| ----------------- | -------------------------------- |
| 46                | 002E                             |

**Table 1.2.1**

| Passphrase Length In HexaDecimal | Length in little endian |
| -------------------------------- | ----------------------- |
| 002E                             | 2E00                    |

Converted string is again wrapped with 16-bit little endiane encoding as show in the Table 1.3

**1.3 -- Message Flag** 
Flag is 16-bit little endian Hexadecimal number.

```
flage = 1000 
flage after performing conversions : E803
```

The final message is created by adding the strings obtained from step Flag,Message Length and . This string will be sent as handshake string and can be seen in table 1.5.

**Table 1.0.1**

| Flag | Length | Passphrase                                                   |
| ---- | ------ | ------------------------------------------------------------ |
| E803 | 2E00   | 2F4D656469615061726B2D426C6F636B636861696E2D50726F746F747970653A323032302F31302F31383330332F |

To further elaborate this string, First column contains the flag second length of the passphrase and third column contains the passphrase message.
