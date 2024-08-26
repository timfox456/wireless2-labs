# Section 8: Dynamic Encryption Keys Lab

## Review Questions

[Review Questions](review08-dynamic-encryption.md)


## Lab

Wi-Fi Protected Access (WPA) is a security implementation and certification
prior to the release of the 802.11i standard. WPA has two modes, Personal and
Enterprise, and uses TKIP/RC4 encryption algorithms. WPA2 is a certification
that is fully compliant with the 802.11i standard. It, too, has two modes,
Personal and Enterprise. WPA2 uses AES/CCMP encryption algorithms. With either
WPA or WPA2 personal, a Passphrase is used to seed the key derivation functions
used by the STA and the AP. The Passphrase along with the SSID are passed
through a key derivation function that outputs a 256-bit Pre-shared Key (PSK)
which is also known as the Pairwise Master Key (PMK). To generate the actual
key used to encrypt traffic between the STA and the AP, a Pairwise Transient
Key (PTK) has to be generated. The PTK is used to encrypt the data payload of
802.11 frames. This data payload, the MSDU, is the layer 3-7 data passed
between the STA and the AP.

The generation of the per-session PTK requires four more pieces of data: The
Authenticator Nonce, Supplicant Nonce, Authenticator MAC, and Supplicant MAC.
The supplicant is synonymous with the client or device. The authenticator is
the access point. A Nonce is a random number, a number used once.

It should be clear at this point that we can determine or use tools to help
find the PTK from the other four pieces of data because the function to
generate the PTK is not a secret. So how do we get the other pieces of data? We
capture it in raw 802.11 form. The process to exchange the data between the AP
and the STA is also known as a four-way handshake. Once we have the handshake,
we can use a cracking tool and a dictionary to try the different letter
combinations in the dictionary to attempt to find the PTK. The PTK can be
reversed mathematically to find the passphrase. Don’t worry, you won’t have to
do any math for this lab. The dictionary is only going to work if the actual
letter combination of the passphrase is in included in there. With this
information, we can deduce that these dictionaries may be large files if it is
going to contain all the letter, number, and special character combinations.

In the capture file, `carWifi.cap`, exists a four-way handshake. `aircrack-ng` is
installed on your Linux workstation. Also bundled with this Linux are wordlists
that serve as dictionaries. There is one called mod_rockyou.txt that is zipped
up in a directory. You will have to unzip it to use it.

## Step 1: Use the `carWifi.cap` file to answer the following questions. 


Although the end goal for this lab is to recover the wireless passphrase, you
can learn a lot about the steps that lead up to the four- way by focusing in on
the target client in the capture. Recall the AKM Soup to Nuts process. It all
begins with the Discovery phase.

1. Sort through the capture file to display only EAPOL (EAP over LAN) messages. How many 4- ways, complete or partial are in this capture? What are the MAC addresses of the client(s) and access point(s)?

2. Sort out the extraneous frames and narrow in on the Motorola client. Create a new display filter (`wlan.addr == <MACofTarget>`). How many frames include the Motorola target?

3. Are there other networks that the Motorola target was connected to recently before participating in the four-way exchange with the new access point? How can you tell?

4. Diagram out the exchange of frames starting with discovery of the new access point and ending with the last frame of the four way. This will help you better understand the whole exchange that occurs between a client and an access point. It should also give you insight into ways that you may be able to take advantage of clients and access points.


## Step 2: Crack the Passphrase

Now let’s get cracking... Research and try to crack the passphrase. This will take some time to finish. Once started, move along and try to answer some of the other questions here. Just check back to see the progress. Generally, you can use the command here with modifications to suit your need for this lab:

```console
aircrack-ng -w mod_rockyou.txt carWifi.cap  # (You may need to specify paths)
```

5. What dictionary did you use and how did you find it? What is the full path
   to the dictionary?

6. What commands did you use to crack the handshake if you were successful?

7. How many keys were tested?

8. If you are successful, what is the passphrase?

9. How do you think the 4-way handshake was captured? Was the forced off? Look
   for any frames that lead up to the authentication phase between the targets.

10. How could we get a four-way exchange if a client was already authenticated
    and associated to our target wireless network?

Once we have found the pre-shared key and if it’s in our plan, we could
authenticate and associate with the target wireless network and start poking
around. Remember tradecraft. What initial situational awareness information can
we get, or should we get?





