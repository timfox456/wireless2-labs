# Lab 1: WLan Components

[Review Questions](./review01-wlan-components.md)




You will use the Wireshark protocol analyzer application to examine some 802.11
radio captures. Wireshark is tool that administrators and penetration testers
can use to analyze traffic and aid in identifying networks and devices.

Open Wireshark from the command line using the command 

```console
sudo wireshark channel6_public.cap 
```

Select the “Wireless” from the top menu bar. Then select “WLAN Traffic.” 


This window shows WLAN statistics for this capture file. Sort the SSID column
and identify the BSSID for the following networks. Remember the BSSID is the
MAC address of the access point.

1. attwifi BSSID:

2. Xfinitywifi BSSID:

3. Starbucks WiFi BSSID:

4. BaguetteCampus (Panera Bread) BSSID:


Next, identify the vendor or manufacturer of the AP based on the OUI of the
BSSID.

5. attwifi Vendor:

6. Xfinitywifi Vendor:

7. Starbucks WiFi Vendor:

8. BaguetteCampus Vendor:

Now that you have identified the MAC addresses of some networks, apply a filter
to determine how many frames are associated with each in the capture. In the
filter window, apply a write a filter to identify the number of frames, using
`wlan.bssid==<xx:xx:xx:xx:xx:xx>`.

The number of selected frames can be found in the lower right of Wireshark. 


Clear the filter after each use with the red X to the right of the filter window.

9. Attwifi:

10. Xfinitywifi:

11. Starbucks WiFi:

12. BaguetteCampus:


Bonus: This capture file was collected in the Augusta, Georgia area. Can you
tell the location within a few hundred feet of where the survey occurred? How
did you find out? Keep in mind how far or limited wireless signals may travel.

