# Week 4 Journal

**Student Name:** Sumita Talukdar Trina

**Topics:** Network Technologies (tutorial) and Internetworking (lecture)

---

## Task 1 - Knowledge Test

![Knowledge Test 04 result](images/KT-04.png)

I did the Week 4 Knowledge Test on Internetworking and got **6.86 out of 10 (68.6%)**.

The question I got wrong was about [topic]. I picked [your answer] but the right answer was [correct answer]. Now I understand it is because [reason].

I used certainty level 1 for all 9 questions because I was not fully sure about my answers. The feedback said I was "a bit under-confident". So next time, if I am sure about an answer, I will pick a higher certainty to get more marks.

---

## Task 2 - Project Initiation

I am not in a group for the project. I am doing the project on my own and I will check this with my tutor.

---

## Task 3 - Network Diagrams

I drew both diagrams in draw.io. I used simple rectangles for the switches and PCs and plain lines for the links (no arrows), like the "Drawing Network Diagrams" guide says.

### a) One switch and four PCs

![Task 3a network diagram](images/week4-task3-lana.png)

Draw.io file: [week4-task3-lana.drawio](images/week4-task3-lana.drawio)

Every PC has its own cable to the switch SW1, so this is a star topology. The switch looks at the MAC address and sends the frame only to the PC it is meant for.

### b) Three switches and eight PCs (star topology)

![Task 3b network diagram](images/week4-task3-lanb.png)

Draw.io file: [week4-task3-lanb.drawio](week4-task3-lanb.drawio)

PC1 to PC4 are connected to SW1, and PC5 to PC8 are connected to SW2. Then SW1 and SW2 both connect to SW-Core in the middle.

One thing I noticed: if PC1 wants to send something to PC5, it has to go PC1 → SW1 → SW-Core → SW2 → PC5. So SW-Core is really important. If it stops working, the two sides cannot talk to each other anymore, but PCs on the same switch still can.

---

## Task 4 - Ping Analysis

I did not have my capture file from before, so I did the ping again. First I cleared the ARP table (I had to open PowerShell as Administrator, otherwise it gave an "requires elevation" error), then I pinged the OpenWRT VM.

```powershell
PS C:\WINDOWS\system32> arp -d *

PS C:\WINDOWS\system32> ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:
Reply from 192.168.1.2: bytes=32 time=2063ms TTL=64
Reply from 192.168.1.2: bytes=32 time=38ms TTL=64
Reply from 192.168.1.2: bytes=32 time=67ms TTL=64
Reply from 192.168.1.2: bytes=32 time=310ms TTL=64

Ping statistics for 192.168.1.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 38ms, Maximum = 2063ms, Average = 619ms
```

All 4 pings got a reply, so the connection is working.

<!-- If you add a Wireshark screenshot later, put it here:
![Wireshark capture](images/week4-task4-wireshark.png)
-->

### Network diagram

![Task 4 ping network diagram](images/week4-task4-ping.png)

Draw.io file: [week4-task4-ping.drawio](week4-task4-ping.drawio)

My Windows computer and the OpenWRT VM (192.168.1.2) are on the same network, connected through the VirtualBox virtual switch. There is no router in between, so the ping goes straight to OpenWRT.

### What is ARP for?

My computer knew the IP address of OpenWRT (192.168.1.2), but that is not enough on a LAN. Ethernet needs the MAC address to deliver the frame. Because I cleared the ARP table, my computer did not know the MAC address anymore, so it had to use ARP first.

- **Who sent the ARP request?** My Windows computer.
- **Who did it go to?** Everyone on the network, because it is a broadcast (destination MAC `ff:ff:ff:ff:ff:ff`).
- **What did it ask?** "Who has 192.168.1.2? Tell [my Windows IP]."
- **The reply:** Only OpenWRT answered, and it sent the reply straight back to my computer with its MAC address.

After that my computer saves the MAC address in the ARP table, so it does not need to ask again for the next pings.

I think this is why my **first ping took 2063 ms** but the others were only 38 to 310 ms. The first one had to wait for ARP to finish.

### ARP packet diagram (1st ARP packet)

![ARP packet diagram](images/week4-task4-arp-packet.png)

Draw.io file: [week4-task4-arp-packet.drawio](week4-task4-arp-packet.drawio)

- Ethernet header = 14 bytes (Destination MAC 6 + Source MAC 6 + Type 2)
- ARP message = 28 bytes
- **Total = 42 bytes**

The Type field is 0x0806, which means ARP. I found it interesting that there is no IP header in the ARP packet. ARP sits directly inside Ethernet because its job is to find the MAC address before IP can be used on the LAN.

### The first two ICMP packets

1. **Echo Request (ICMP type 8)** - my computer sends this to 192.168.1.2. It has 32 bytes of data (you can see `bytes=32` in the output).
2. **Echo Reply (ICMP type 0)** - OpenWRT sends this back to my computer.

The request and the reply have the same identifier and sequence number. That is how ping knows which reply belongs to which request and can work out the time.

I also looked at the **TTL=64** in the reply. Linux usually starts TTL at 64 and Windows at 128, so this shows the reply really came from OpenWRT (which runs on Linux). Also, since it is still 64, the packet did not pass through any router, which matches my diagram.

### ICMP packet diagram (1st ICMP packet)

![ICMP packet diagram](images/week4-task4-icmp-packet.png)

Draw.io file: [week4-task4-icmp-packet.drawio](week4-task4-icmp-packet.drawio)

| Part | Size |
|---|---|
| Ethernet header (Type 0x0800 = IPv4) | 14 bytes |
| IP header (Protocol 1 = ICMP) | 20 bytes |
| ICMP header | 8 bytes |
| ICMP data | 32 bytes |
| **Total** | **74 bytes** |

This shows encapsulation. The ICMP message (8 + 32 = 40 bytes) is inside the IP packet (20 + 40 = 60 bytes), and the IP packet is inside the Ethernet frame (14 + 60 = 74 bytes). Each layer just adds its own header in front.

---

## Reflection

Before this week I thought ping was just one simple thing, but it actually uses two protocols: ARP to find the MAC address and ICMP for the ping itself. The lecture was about how IP and routing tables send packets across different networks, and this task showed the other side: even on the same LAN, the computer still needs the MAC address.

The part that made it click for me was the slow first ping. I cleared the ARP table, and the first reply was much slower than the rest, which makes sense now.

Drawing the packet diagrams also helped me understand encapsulation. The ping only sends 32 bytes of data, but with all the headers it becomes 74 bytes.

The tricky part for me was [write something that was hard for you, e.g. getting the admin PowerShell to work, or understanding why ARP has no IP header]. Next time I want to capture the packets in Wireshark as well, so I can see the ARP and ICMP packets myself instead of only the ping output.
