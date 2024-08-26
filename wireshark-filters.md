# Wireshark Filters


| Decription                                 | Filter                          |
|--------------------------------------------|---------------------------------|
| Select only frames from a BSSID            | `wlan.bssid == <BSSID>`         |
| Sort by wireless network name              | `wlan.ssid == "Guest"`          |
| Sort on Frame Control Types                | `wlan.fc.type == #`             |
| Sort on Frame Control Subtypes             | `wlan.fc.type_subtype == #`     |
| Management frames                          | `wlan.fc.type == 0`             |
| Control frames                             | `wlan.fc.type == 1`             |
| Data frames                                | `wlan.fc.type == 2`             |
| Association request                        | `wlan.fc.type_subtype == 0`     |
| Association response                       | `wlan.fc.type_subtype == 1`     |
| Reassociation request                      | `wlan.fc.type_subtype == 2`     |
| Reassociation response                     | `wlan.fc.type_subtype == 3`     |
| Probe request                              | `wlan.fc.type_subtype == 4`     |
| Probe response                             | `wlan.fc.type_subtype == 5`     |
| Beacon                                     | `wlan.fc.type_subtype == 8`     |
| Announcement traffic indication map (ATIM) | `wlan.fc.type_subtype == 9`     |
| Disassociate                               | `wlan.fc.type_subtype == 10`    |
| Authentication                             | `wlan.fc.type_subtype == 11`    |
| Deauthentication                           | `wlan.fc.type_subtype == 12`    |
| Action frames                              | `wlan.fc.type_subtype == 13`    |
| Block ACK Request                          | `wlan.fc.type_subtype == 24`    |
| Block ACK                                  | `wlan.fc.type_subtype == 25`    |
| Power-Save Poll                            | `wlan.fc.type_subtype == 26`    |
| Request to Send                            | `wlan.fc.type_subtype == 27`    |
| Clear to Send                              | `wlan.fc.type_subtype == 28`    |
| ACK                                        | `wlan.fc.type_subtype == 29`    |
| Posting of HTTP information                | `http.request.method == "POST"` |
