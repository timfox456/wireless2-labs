# Section 12: Security Risks and Threats Lab


## Review Questions

[Review Questions](review12-security-risks.md)


## Lab

Before we dive into some more command line, ensure the Panda Wireless card is
connected.

If using VirtualBox, this is done by selecting from the top menu of the running
VirtualBox machine: Devices, USB, Ralink 802.11 n WLAN, Connect (Disconnect
from host). If done correctly, then a check mark will appear next to it. To
disable, uncheck the box. We need it enabled for these labs.


## Step 1: Find copies of the `oui.txt`

There are multiple copies of oui.txt document on these Linux machines. This
document has the OUI for each vendor associated with a network interface
cards—wireless or wired. Find each oui.txt on your local Linux box and place a
copy of the largest one on your desktop. The following command will find it,
count the number of lines in the file and display it to you with a path. The
higher the line count, the greater the entries that are found in it.

```console
sudo find / -iname oui.txt -exec wc -l {} \;
sudo find / -iname oui.txt | xargs wc -l
sudo find / -iname oui.txt | xargs ls -l
```


What is the absolute path of the original local copy of ieee oui.txt? 

```console
cp <path to largest ieee oui.txt> ~/Desktop/.
```

## Step 2: Locate MAC Address

Locate the MAC address(es) of your virtual Linux box. The MAC address is
labeled “ether.” 


```console
ifconfig -a
```

What is the 48-bit MAC address(es) of your wireless card?


## Step 3: Attempt to find Adapter Info

Search through the oui.txt file to locate the manufacturer and address of your
adapter(s). Even if you search correctly, you should not find it. Why do you
think this is the case? 

```console
grep -i <yourOUI> ~/Desktop/oui.txt (Replace the OUI)
```


## Step 4:  Use `macchanger`

Many networks use a white list of allowed or permitted MAC addresses that can
associate with a wireless network. It is used in many guest hot spots as an
authorization method. MAC filters and white lists can still be found implement
in enterprise networks despite the ease of bypassing them. Use macchanger and
its help to view your current/permanent MAC address of your wireless interface.
Change your wireless interface MAC address to a randomly generated one, then
back to the permanent one. 

After each step, verify any changes with `ifconfig` command. Try and figure out
the usage and do so. You will likely have to turn off the wireless interface
before switching any of the properties if you get a “device busy” error
message. After you change the MAC, be sure to turn it back on.

```console
macchanger --help
sudo macchanger [options] device
sudo ifconfig <yourDevice> down/up
```

Step 5 
5. What commands did you use to view the current MAC address, change it, and
   revert to the permanent MAC?

6. What was the MAC address of the wireless adapter before, permanent, and
   after?

7. Research where to find and what the timeout is set as for your local ARP
   cache. Having this knowledge will help you if you ever attempt a
   man-in-the-middle attack. What is the path of the file you found and what is
   the current value? Hint: 7th section man page for `arp`. 

```text
ARP(8)                               System Manager's Manual                              ARP(8)

NAME
     arp – address resolution display and control

SYNOPSIS
     arp [-n] [-i interface] hostname
     arp [-n] [-i interface] [-l] -a
     arp -d hostname [pub] [ifscope interface]
     arp -d [-i interface] -a
     arp -s hostname ether_addr [temp] [reject] [blackhole] [pub [only]] [ifscope interface]
     arp -S hostname ether_addr [temp] [reject] [blackhole] [pub [only]] [ifscope interface]
     arp -f filename

DESCRIPTION
     The arp utility displays and modifies the Internet-to-Ethernet address translation tables
     used by the address resolution protocol (arp(4)).  With no flags, the program displays the
     current ARP entry for hostname.  The host may be specified by name or by number, using
     Internet dot notation.

```


Reboot the physical or virtual machine to revert the MAC address you changed.
As an alternative, use macchanger to set it back to its permanent MAC.


In this exercise, you will create a software-based access point using the
command line tool `hostapd` and `dnsmasq` and bridge it to an existing network
interface with Internet access in VM. This technique can be used to set up an
evil twin access point. Read each step before completing. Do not forget to do
the last numbered step, at the end with iptables.

1. This setup uses a VirtualBox ParrotOS guest that already has a bridged
   connection via the host operating system and its connection to an existing
   wired or wireless network. Inside the virtual guest, it is assumed that
   internet connectivity is through the first Ethernet connection, eth0. Verify
   the connection and Internet connectivity. Troubleshoot as needed.


Run `eth0` with an IP addrss: 
```console
ifconfig
```


Check for your wireless card:
```console
iwconfig
```

This will verify your DNS and Internet Connectivity

```console 
ping www.google.com
```


2. Verify the command line tools hostapd and dnsmasq are installed. If you get
   an executable path for each after running the following, skip step 2 and
   proceed to creating the configuration files in step 3.


```console
# which hostapd dnsmasq (Will show a path to each if installed)
```

Only do these remaining steps if hostapd and dnsmasq are NOT installed. Install
hostapd and dnsmasq through the APT package manager as root. These steps will
generate a lot of textual output and will likely prompt you during the update
and install for some action on your part. Read the messages, choose wisely.

First, we need to update the repository information in the file:
`/etc/apt/sources.list.d/parrot.list`. Change the only uncommented line to read
exactly as the following, save changes, and exit the editor.

```text
deb http://mirrors.mit.edu/parrot/ rolling main contrib non-free
```

The install will pause a few times while you install the tools. The first
message is just informational, choose “q” to quit. The second is about
restarting services, choose “yes.” The next pause is prompting you for a
decision on what to do about a configuration file and an updated version,
choose N to keep the currently-installed version.

```console
apt update (Updates your repository information)
apt install dnsmasq hostapd (Will install or update the existing packages)
```


## Step 3: Create configuration file

Next, create an access point configuration file for hostapd using an editor in
your home directory. There is a large template configuration file with
everything imaginable as a reference located in the directory:
`/usr/share/doc/hostapd/examples`. It is zipped up as `hostapd.conf.gz`. 


You only need 4 lines to configure the first part of your software-based access
point. These four lines will create an open wireless network. Later you will
implement the use of encryption. You will create a text file using your
favorite text editor for Linux.

Create a configuration file in your home directory with the visual editor:

```console
vim hostapd.conf
```

```text
interface=wlan0  
driver=nl80211
ssid=         # You choose a network name 
channel=      # You choose a 2.4 GHz channel 1-11

# LINES BELOW THIS POINT ARE OPTIONAL, THINK BEFORE YOU UNCOMMENT 
# Lines with # are treated as comments and ignored in the configuration file

# beacon_int=100              # (0.1024 sec. This is the default and not needed) 
# ignore_broadcast_ssid=1     # (Uncomment to cloak your network)
# hw_mode=a                   # (Lines with # are treated as comments)
# ieee80211n=1                # (To use 802.11n specifications)
# channel=36                  # (First 5GHz channel, followed by 40, 44, and 48)
```

To add encryption to your wireless network, add these next few lines to some
configuration file above. As an example, you would do this to mimic the setting
of your target network when setting up an evil twin access point.

```text
auth_algs=3                   # (Open System Authentication)
wpa=2                         # (WPA2)
wpa_key_mgmt=WPA-PSK          # (Personal, passphrase, preshared key) 
rsn_pairwise=CCMP             # (CCMP-Advanced Encryption Standard) 
wpa_passphrase=P@ssw0rd!      # (Same as your target or whatever)
```


## Step 4: Configure Wireless Card Into Monitor Mode

4. Configure your wireless card into monitor mode and then a static IP address and route.

```console
airmon-ng check kill   # (Kill wpaclient that might cause problems) 
ifconfig wlan0 down
iwconfig wlan0 mode monitor
ifconfig wlan0 up 10.99.0.1 netmask 255.255.255.0
route add -net 10.99.0.0 netmask 255.255.255.0 gw 10.99.0.1
```


## Step 5: Print Routing Table to get gateway for network

Print the routing table and confirm a gateway for `10.99.0.0/24` network
through wlan0. You should see in the output under the Destination column,
`10.99.0.0`, with a Gateway of `10.99.0.1` and Flags UG for the interface
`wlan0`.

```console
route -n   # (Prints the routing table)
```

## Step 6: Create configuration for `dnsmasq` 

Create a DNS and DHCP configuration file for the dnsmasq tool inside the same
directory as you did with the previous hostapd configuration file. Use your
favorite editor. Name the file `dnsmasq.conf`. Your software access point will
act as the default gateway and DHCP server. The DHCP server will hand out IP
addresses (options) for a DNS server and the gateway for victim clients.

 Create a configuration file in your home directory with the visual editor: 

```console
# vim dnsmasq.conf # (Add the lines below)
```

```text
interface=wlan0 
dhcp-range=10.99.0.2,10.99.0.100,255.255.255.0,1h
dhcp-option=3,10.99.0.1 
dhcp-option=6,10.99.0.1 
server=8.8.8.8 
log-queries
log-dhcp 
listen-address=127.0.0.1
```


## Step 7: Run service to log wlan events in background

You will need two separate terminals to run each of the following commands.
Start the software access point and DNS/DHCP servers using the new config
files. When you execute these commands, they will run in the foreground and
will produce textual output when a client joins the network along with any DNS
and resource names that get resolved. Think about how you might use this
information from an attack perspective. Stopping these services is accomplished
by sending the `SIGTERM` signal (`CTRL-C`).

```console
hostapd ./hostapd.conf       # (Assuming the config file is in the current directory) 
dnsmasq -C ./dnsmasq.conf -d
```


## Step 8: Configure IP forwarding and IPTables rules

In a third terminal window configure IP forwarding and IPTables rules. The
following lines set up network address translation (NAT) across the wired
interface, eth0. They also bridge wlan0 and eth0 together and enable routing
between both networks. It needs to be understood that these commands and action
will not persist across reboots.

```console
iptables --table nat --append POSTROUTING --out-interface eth0 -j MASQUERADE 
iptables --append FORWARD --in-interface wlan0 -j ACCEPT
echo 1 > /proc/sys/net/ipv4/ip_forward
```


