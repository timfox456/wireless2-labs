
## Comparison Between OSI model and DOD / TCPIP

OSI is a historical artifact,  obsolete.
DOD/ TCP_IP is used by wireshark


| Layer | ISO/OSI      | DOD / TCP_IP          |
|-------|--------------|-----------------------|
| 7     | Application  | L4: Process           |
| 6     | Presentation | ^                     |
| 5     | Session      | ^                     |
| 4     | Transport    | L3: Host-to-Host      |
| 3     | Network      | L2: (Inter) Network   |
| 2     | Datalink     | L1: Device Attachment |
| 1     | Physical     | ^                     |
