# Bonus Lab: Attacking Wi-Fi Protected Setup (WPS)

## Background

Wi-Fi Protected Setup (WPS) was designed to make it easy for users to add new devices to their wireless network without needing to enter a long passphrase. Instead, they can connect using an 8-digit PIN printed on a sticker on the router.

While convenient, this created a severe design flaw. The 8-digit PIN is validated in two halves. The first four digits are checked separately from the last four. Furthermore, the 8th digit is just a checksum of the first seven. This drastically reduces the number of possible combinations from 100,000,000 (10^8) to only 11,000 (10^4 + 10^3). This weakness makes a brute-force attack against the PIN practical, often taking only a few hours to complete.

In this lab, you will use the `reaver` tool to exploit this vulnerability to recover the WPA/WPA2 passphrase for the Belkin network without needing to capture a 4-way handshake.

**Objective:** Use a WPS brute-force attack to recover the network passphrase.
**Target:** The Belkin wireless network.

---

## Step 1: Attach Wireless Card and Enter Monitor Mode

As with previous labs, ensure your wireless card is attached to the Parrot VM and that it is in monitor mode.

1.  Attach the wireless card via the VirtualBox `Devices -> USB` menu.
2.  Kill any processes that might interfere with the wireless card.

    ```console
    sudo airmon-ng check kill
    ```

3.  Start monitor mode on your wireless interface. This will create a new interface, likely named `wlan0mon`.

    ```console
    sudo airmon-ng start wlan0
    ```

## Step 2: Identify WPS-Enabled Networks

Before launching the attack, you must confirm that the target AP has WPS enabled. The `wash` tool is designed specifically for this purpose. It scans the area and lists only WPS-enabled networks.

1.  Run `wash` on your monitor interface.

    ```console
    sudo wash -i wlan0mon
    ```

2.  Examine the output. You are looking for the BSSID and Channel of the Belkin network. The `WPS Locked` column is also important. If it says `Yes`, the AP has likely locked out WPS functionality due to previous failed attempts. If it says `No`, you can proceed with the attack.

    ```console
    BSSID               Ch  WPS Version  WPS Locked  ESSID
    ----------------------------------------------------------------
    XX:XX:XX:XX:XX:XX   6   1.0          No          Belkin-Network
    ```

    *Note: If the Belkin network does not appear in the `wash` scan, it is not vulnerable to this attack. You can still proceed with the lab steps to understand the process, but the attack itself will fail.*

## Step 3: Launch the Reaver Attack

Once you have the BSSID and channel of the WPS-enabled target, you can launch the `reaver` tool. `Reaver` will methodically test PIN combinations against the AP until it finds the correct one.

1.  Use the following command to start the attack. You will need the BSSID and channel you discovered in the previous step.

    *   `-i wlan0mon`: Specifies the monitor interface.
    *   `-b [BSSID]`: Specifies the target's BSSID.
    *   `-c [Channel]`: Specifies the target's channel.
    *   `-vv`: Enables double verbose output to see the attack progress in detail.
    *   `-K 1`: An alternative cracking method, sometimes more effective.

    ```console
    sudo reaver -i wlan0mon -b <BSSID_of_Belkin_AP> -c <Channel_of_Belkin_AP> -vv -K 1
    ```

2.  Let `reaver` run. You will see it trying different PINs and reporting its progress. The tool automatically saves its session, so if you need to stop and restart it (`CTRL-C`), it will resume from where it left off.

    ```console
    [+] Trying pin 12345670.
    [+] Sending EAPOL START request
    [+] Received identity request
    [+] Sending identity response
    ...
    [+] PIN cracked in 1337 seconds
    ```

## Step 4: Analyze the Results

If the attack is successful, `reaver` will report the correct WPS PIN and, more importantly, the WPA/WPA2 passphrase for the network.

1.  Once the attack completes, look for the following output:

    ```console
    [+] WPS PIN: '12345678'
    [+] WPA PSK: 'TheSecretPassphrase'
    [+] AP SSID: 'Belkin-Network'
    ```

2.  Record the recovered passphrase. You have now successfully broken the network's security using a different method than the 4-way handshake capture.

## Questions

1.  How does the WPS attack differ from the passphrase cracking attack used in Lab 8? What are the advantages of the WPS attack?
2.  What action could the network administrator take to prevent this attack?
3.  During the attack, `reaver` may report warnings like `WPS transaction failed (code: 0x02), re-trying last pin` or `Detected AP rate limiting`. What do these messages indicate?
