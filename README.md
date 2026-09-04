# Creating Custom Packets and Generating Traffic

For this lab, I used Scapy and Hping3 on Kali Linux to manually craft and send network packets, then used Wireshark to capture and inspect that traffic. The goal was to understand how network packets are actually built at a low level, rather than relying on standard tools that construct and send packets automatically, and to see that traffic reflected in a packet capture.

## Step 1: Installing the necessary tools

I started the Kali VM and installed Scapy, a Python-based tool for crafting and manipulating network packets:

```
sudo apt update
sudo apt install scapy
```

<img width="1887" height="833" alt="image" src="https://github.com/user-attachments/assets/d2f94275-4e89-426c-81cf-6f937fceac3b" />

## Step 2: Creating custom packets with Scapy

I launched Scapy's interactive shell:

```
sudo python3 -m scapy
```

<img width="929" height="438" alt="image" src="https://github.com/user-attachments/assets/013fda6e-6254-43ec-b8b1-2e95272dd893" />

From within Scapy, I built a custom ICMP packet using Python syntax, specifying the destination IP address and stacking the ICMP layer on top of the IP layer:

```
from scapy.all import *
packet = IP(dst="8.8.8.8")/ICMP()
send(dice_packet)
```

This builds the packet by stacking two layers together: an IP layer addressed to 8.8.8.8, and an ICMP layer on top, which represents an echo request, the same type of request a normal ping sends. Building it this way just makes each part of the packet visible before it's sent, instead of it happening automatically in the background like a regular ping does. The send() function then sends the packet, and Scapy confirms it with a Sent 1 packets. message.

<img width="670" height="141" alt="image" src="https://github.com/user-attachments/assets/25169611-402a-402a-88be-7eeaa515ee61" />


## Step 3: Generating custom traffic using Hping3

I used Hping3 to generate TCP SYN traffic directed at a target IP address on a specific port:

```
sudo hping3 -S -p 443 -c 5 10.0.2.4
```

This command sends 5 TCP packets with the SYN flag set to port 443 (HTTPS) on the target machine. Unlike a normal connection attempt, Hping3 lets each of these parameters be set explicitly, which is useful for testing how a target host or firewall responds to specific types of traffic without needing a full application-layer connection.

<img width="785" height="201" alt="image" src="https://github.com/user-attachments/assets/bb5ea147-f47f-4711-a746-a8d93424ae25" />


## Step 4: Capturing and analyzing packets using Wireshark

I opened Wireshark, selected the `eth0` interface, and started a capture. To narrow the results down to the traffic generated in the previous steps, I applied the following display filter:

```
tcp.port == 443
```

<img width="1403" height="837" alt="image" src="https://github.com/user-attachments/assets/27e4698b-a2f3-4b09-9250-dd2b7453f217" />


The filter applied successfully and the capture was actively running on the correct interface. However, the VM stopped responding before I was able to re-run the Hping3 traffic while the capture was live, so I wasn't able to confirm the SYN packets appearing in the capture itself. I plan to redo this step by starting the Wireshark capture first, then generating the Hping3 traffic afterward so the packets are caught in real time.



## Conclusion

This lab covered packet creation and traffic generation using Scapy and Hping3, both of which completed successfully. Constructing the ICMP packet layer by layer with Scapy made it clear how much control exists over what actually goes into a packet, from the IP header down to individual fields, rather than treating network traffic as something only generated automatically by applications. Generating SYN traffic with Hping3 further showed how specific packet types and flags can be sent on demand for testing purposes. The final step, capturing that traffic live in Wireshark, wasn't completed due to the VM freezing partway through, but the filter and capture setup were confirmed working correctly beforehand.
