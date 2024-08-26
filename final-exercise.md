# Wireless Networking II Security Final Exercise

## Background: 

You are a wireless network auditor tasked to assess your coffee company’s
wireless network security using a black box approach with minimal knowledge of
the target network. Proper prior planning is important to do this successfully.
The goal is to gather as much information about the network first by passive
means and when possible, through active means. This will likely include
“breaking into the network” to assess the feasibility of such an approach. Once
a client in the network, gather more information about what is found there. Try
to imagine what information would be helpful to a legitimate attacker targeting
the company’s network. Can you get out to the internet or other network
resources once a client of the network? What other targets might be in the
wireless network besides the access point and yourself.

## General Steps for this Assessment:

1.  Passive discovery with monitor interface to identify your target network, setting, and clients

Tools: airmon-ng & airodump-ng

2. Narrow down your targets by channel and MAC address

Tools: airodump-ng

3. Select a client to kick off to capture a 4-way in a saved file

Tools: airodump-ng & aireplay Break the passphrase

4. Break the Passphrase

Tools: aircrack-ng & mod_rockyou.txt

5. Join the wireless network as a client using a managed mode wireless interface
j
Tools: Parrot GUI or command line

6. wpEnumerate as much information about the network and any resources as a client

Tools: web browser and/or command line tools
