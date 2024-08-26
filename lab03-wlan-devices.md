# Section 3: WLAN Devices and Architecture

## Review Question

[Review Questions](review03-wlan-devices.md)


## Lab


We will be using the USB wireless cards. It is important to know the details of the model of the wireless card you will be using for pentesting and network evaluations.


## Step 1: FInd model number and MAC Address

Note the model number and MAC address of the wireless card if you can find it: 


1. Model # __________________________________________________________ 
2. MAC Address: _____________________________________________________

## Step 2 : Get Chipset Information

Plug in the wireless card into a USB port. After you plug it in and it settles down, open up a terminal window and run lsusb to get the chipset information. The lsusb command list devices that are connected to the USB bus. To get a verbose output, try -vv.

```console
# lsusb
```


## Step 3: Get Driver Information

Next, find the driver associated with the wireless card(s) using airmon-ng.
Displayed as PHY, Interface, Driver, Chipset

```console
airmon-ng:
```



Now that we have an idea about our driver and chipset, visit the [`aircrack-ng`
website](https://www.aircrack-ng.org/doku.php?id=compatible_cards) and see if
your wireless adapter is compatible with Linux for wireless pentesting and
surveys.

Newer versions of the Linux kernel support newer wireless drivers. With new
kernels and drivers come newer command line configuration tools. The command
`iwconfig` is being replaced by the newer `iw` command. The `iw` command is
used to show or manipulate wireless devices and their configurations.

## Step 4: Use `iwlist` command to view Frequncies

Use the `iwlist` command, which is one word and slightly different from `iw`,
to view the frequencies supported by your wireless adapter. Also, find the
transmit power.

```text
# iwlist <your interface> frequency
# iwlist <your interface> channel
# iwlist <your interface> txpower ____________________
```

Hint: You may need to turn on the wireless adapter to see `txpower`, or try
running the command a few times to see any results. If not, run `iwconfig` again
and look at the output.

## Use `iwlist` to get detailed info

 Read the detailed information about options and WLANs in the RF vicinity.

```text
iwlist <your interface> scanning
```

1. How many SSIDs are visible? 
@. How many bands/frequencies are available? 
