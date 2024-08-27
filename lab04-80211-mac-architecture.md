# Section 4: 802.11 MAC Architecture Lab

## Review Questions

[Review Questions](review04-80211-mac-architecture.md)


## Lab

The next set of questions can be answered with the `umbctc_2.4.cap` capture
file. As you examine the file searching for the answers, keep in mind that
`802.11` RF based networks work using the `CSMA/CA` method. Wireless network
captures only give you insight into the network from where you surveyed the
data. Meaning that we might not "hear" both sides of a conversation because of
where our physical location was at the time of the survey.

Use some of the Wireshark filters from the slides and from the back of this
handout as you go through the capture file. Try and combine some with Boolean
logic (AND, OR, NOT). 

[Wireshark Filters Cheat Sheat](wireshark-filters.md)


## Step 1: Answer the Following Questions

Start by examining frame #3. This is a management type frame, specifically a
beacon that originates from an access point. Open up each field in the packet
details pane.


1. What is the Subtype under the Frame Control Field?

2. Right-click the Subtype and apply as a selected display filter. This should sort out and display only beacon frames in the capture file. Notice the new display filter near the top in green. 

How many frames out of the entire capture are beacons? When you’re done with the display filter, be sure to clear it.

3. What is the destination MAC address? What is the source MAC address?

4. Examine the IEEE 802.11 Wireless LAN, Fixed Parameters section. What is the Beacon Interval? On what channel does this AP operate?

5. Look at the Traffic Indicator Map. How many clients by AID have data waiting at the AP?

6. Open up the Tagged Parameters. Based on the information found here, what type of AP are you looking at? Specifically, is this an 802.11b, g, a, or n access point? How can you tell?

7. What can learn about the manufacturer and chipset of the AP and its radio(s)?


## Step 2: Answer these questions from frame 102

For the next set of questions, look at frame #102. This is a probe request that comes from a client looking for a wireless network.

8. What type for probe is here? What is the SSID of the wireless network for which the client seeks?

9. On what channel was this probe sent?

10. Similar to questions 6 and 7 from earlier, what type of client is this?

11. What access point(s) responds to the request seen in frame #102?


## Step 3: Answer the following from frame #639

Look at frame #639. What type of management frame is this? Characterize what this station is doing?

Apply a display filter to sort out all wireless frames associated with the client’s MAC address. This will help put into context what we seen about this client.

```text
wlan.addr == <MAC of target client>
```

12. What is happening before and after this probe request regarding this client? Why is there a probe request despite this client already associated with an access point?

## Step 4: Answer the following from frame #577

Examine frame #577. Apply a new display filter to sort for this specific Apple device by MAC.

13. What type of management frame is seen here in #577?

14. Was this Apple device previously connected to another access point before it started talking to the Ubiquiti access point in #577? Why do you think occurred?

15. Try to follow the sequence of events with the Apple device after frame #577. Why did we not see the authentication request prior to #577. Does the Apple client and this new access point exchange any data frames beyond any management frames?

## Step 5: Answer the following from frame #6610

16. Jump to frame #6610. Another unique management frame. What just happened? Did anything precede the disassociation between these two radios?

17. What side of the communication initiated the disassociation in frame #6610 and why?

18. The access point in frame #6610 has what wireless network name? What sort of security settings are being advertised out by the access point? This may require you to apply some more filters to get the information.

19. Sort through the entire wireless capture file for all Requests-to-Send and Clear-to-Send control frames. How many in total are there in the entire capture? You can apply one filter note the count then apply another filter and count those, then add them together.


## Step 6: Answer the following from frame #556 and #557

For the next portion, look at frames #556 and #557.

20. What amount of time has been calculated by the requestor?

21. After the request, you see a Clear-to-Send frame. Why the difference in duration times?
