# Lab 17: WLAN Troubleshooting

General Steps to Capture a 4-way Handshake and Crack ‘Em

1. Ensure your wireless card supports monitor mode and packet injection. Verify
   the operating system also recognizes the wireless card. Then, set your
   wireless card into monitor mode.

```console
iwconfig
aireplay-ng --test wlan0 # airmon-ng start wlan0
```

(Verify the wireless card is seen e.g. wlan0) (Test packet injection)

(Starts monitor mode on wlan0)

2. After you put your card into monitor mode with airmon-ng, it will create a
   newly named interface, e.g. wlan0mon. Verify with iwconfig. Start a capture
and check out the wireless networks and associated clients. Associated and
unassociated clients show up in the bottom of the airodump-ng terminal under
the STATION column. If the clients are part of a wireless network, they will
have the BSSID to the left of its MAC address. Otherwise clients are “not
associated” but are probing for networks. Directed client probes will show up
in the bottom of the terminal under the Probe column. These probed for networks
are previous wireless networks a client has been associated with in the past.
Great information for client pattern of life analysis.

```console
iwconfig (You should see a newly named interface, wlan0mon) 

airodump-ng wlan0mon (By default in 2.4GHz on the new monitor interface)
```

(Other useful options are `--band` and `--channel`)

3. Once you are satisfied with the general network reconnaissance through
   `airodump-ng`, close in on your target network and the client. Look for SSID
(`--essid`), channel, and BSSID (`--bssid`). Modify your capture parameters and
write (-w) the files to the current directory. If you are doing the capture
with root privileges, you may need to change the ownership (chown) of the
capture file if you plan on using it with another user account.

```console
# airodump-ng --channel <ch#> -w <mytarget> wlan0mon
```

4. Look for your target client. Note the MAC address of the client and the
   BSSID of the access point. You will deauthenticate both MAC addresses from
the each other. The goal is to simply kick the client off just briefly to see
it reassociate to the same wireless network. The reassociation process will
include the 4-way dynamic key generation. If your listening device is in the
correct location, you can pick up all four messages.

5. Target the client and AP with the aireplay-ng tool and a deauthentication
   attack (--deauth). Ensure your parameters are correct and you still
capturing and saving the wireless frames, otherwise you will not have a
handshake to use later in the cracking process.

```console
# aireplay-ng --deauth 1 -c <MACofClient> -a <MACofAP> wlan0mon
```


6. Look for the “WPA Handshake: ” message at the top of `airodump-ng` session. This is your visual clue that your listening device was in the proper location to hear the 4-way exchange. If you don’t get the visual indicator, you might not have captured all four messages, or you may have to reinitiate the attack. Opening up the capture file in Wireshark will let you know what just happened and whether you were successful. You should also take a few moments and examine any reason codes that were sent with the deauthentication frames.

7. Kill your `airodump-ng` session (CTRL-C) and open up the capture file with Wireshark. Apply a couple of display filters to narrow in on your target.

```console
$ sudo wireshark mytarget.cap (Do this as a sudoer, not root)
```

Use the display filter `“wlan.fc.type_subtype == 12”` to show only the deauthentication frames. Look inside the packet details section and expand the Fixed parameters subsection under IEEE 802.11 wireless LAN section. What reason code is there?

Clear the display filter and apply a new one but this time filter for “eapol”
wireless frames. If your work in the previous step was done correctly and you
were in the right location, you should have the frame components that comprise
the 4-way handshake. Remember, it is during the 4- way that information is
shared between the client and the access point that is subsequently used to
derive the dynamic encryption keys (PTK) and to share the group temporal key
(GTK) with the associated client. With a clean 4-way handshake, proceed to the
next step.

8. Now you should have enough information to try and attempt to recover the wireless network’s passphrase. The whole point of doing this is so that you can then later use the passphrase to allow your attack platform to join the network and proceed with your next phases of the offensive methodology. For example, you can join the target network and begin to scan for other IP address and targets. You could also use this wireless network to get back out to the internet to proceed with a third-party attack.

The passphrase recovery process (cracking) will take some time to accomplish.
You will need the capture file with the target 4-way handshake. You will also
need a wordlist that serves as a dictionary with possible passphrases. If the
dictionary is large enough and the target’s wireless passphrase is in
dictionary, aircrack-ng will find it. For the time being, point aircrack-ng to
the capture file with your wordlist and grab some coffee or make a sandwich.
This may take a while depending on your local hardware resources.

```console
# aircrack-ng -w </path/to/wordlist> <your4-way.cap file>
```

If `aircrack-ng` is successful, it will show up in the terminal output. The
passphrase recovered is then used to join the wireless network, either manually
or through your client utility.


## Pull off a Layer 2 Denial of Service Against an Attached Wireless Client

1. Attach a wireless card to the Guest Operating system. Your choice for a wireless card should include those that support the functions of packet injection and monitor mode. You will use both functions to pull off a Layer 2 DoS. Verify the card is recognized.

```console
# iwconfig (See that a device wlan0 appears)
```

2. Set the wireless card into monitor mode.

```console
# airmon-ng start wlan0 (This creates a new wireless device, wlan0mon)
```

3. Listen to the airwaves for your target AP (BSSID) and client (Station). Note the MAC address of each. These will be used later with the aireplay-ng tool.

```console
# airodump-ng wlan0mon
# airodump-ng --band a wlan0mon
# airodump-ng --channel 1,6,11 wlan0mon
# airodump-ng --bssid <MACofAP> --channel <Channelof AP> wlan0mon
```

4. You need the two MAC addresses from the previous step. Aireplay-ng supports
   a number of attacks including a deauthentication attack (--deauth). Both
ends will receive deauths spoofed from the other side of the current layer two
association. For example, the client station will receive a series of deauth
frames from your wireless card using the spoofed MAC of the access point and
vice versa. 64 frames are sent in both directions.

```console
# aireplay-ng --deauth 1 -a <MACofAP> -c <MACofClient> wlana0mon
```


5. A count of one for the deauthentication attack can also be used to push a
   client off a wireless network temporarily. The client will then try to
rejoin the same network. During this, the SSID is used in a probe request frame
from the client. This frame would give up the true wireless network name if
SSID cloaking was in use. You could use this attack to push a client off the
WPA/WPA2 network to capture a 4-way handshake (EAPOL) during its reconnection
attempt. You will need to save the airodump-ng capture to do this step.


## Examining Credentials Posted During an HTTP Session on the Management Interface

1. Open up a wireless capture that has upper layer information (L3-7) with the
   Wireshark tool. You will need to search through the capture and look for the
“posting of credentials.” Apply a display filter to accomplish this.

```text
http.request.method == “POST”
```

2. We need to sort through the resulting TCP POSTs. Right click on the first
   filtered frame. In the pop-up menu, select Follow, then TCP Stream. A new
window should pop-up with the reassembled HTTP session information. Red text
indicates the client side and blue text indicates the response from the server.
You are looking for the key word “Basic” in the client-side communication.
Basic is an indication of Base64 encoding.

