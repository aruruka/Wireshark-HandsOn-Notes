# Getting Started with Wireshark - Lab 2 - Display Filters

Lab Questions:
Set a filter for the following traffic types. How many packets do you get?

1. DNS packets (only dns, no ICMP)

    Display filter: `dns and !icmp`.
    Answer: 52

2. DNS packets including the word "foundation" (regardless of case)

    Display filter: `dns and !icmp frame matches "foundation"`
    Display filter: `dns matches "foundation" and !icmp`
    Answer: 34

3. DNS requests for domains that do not have the word "foundation"

    Display filter: `dns.qry.name and not frame matches "foundation"`
    Display filter: `dns and !dns matches "foundation" and !icmp`
    Answer: 18

4. ICMP packets (Extra credit, what port is not reachable?)

    Display filter: `icmp.type == 3 or icmp.type == 4 or icmp.type == 5 or icmp.type == 11 or icmp.type == 12`
    
    **Explanation:**
    ```plaintext
    icmp.type == 3 or icmp.type == 4 or icmp.type == 5 or icmp.type == 11 or icmp.type == 12
    ```

    This includes:
    - Type 3: Destination Unreachable
    - Type 4: Source Quench
    - Type 5: Redirect
    - Type 11: Time Exceeded
    - Type 12: Parameter Problem

5. TCP Port 443

    Display filter: `tcp.port == 443`
    Answer: 885

6. How many packets are in the top IP conversation?

    Statistics > Conversations > IPv4 > click on the "Packets" header to sort by packets descending.
    Answer: 481

7. What is the stream ID? Build a filter for this stream and apply it. 

    Answer: stream ID is 3.
    Display filter: `tcp.stream eq 3`

8. In this stream, how many packets are larger than 100 bytes?

    Display filter: `tcp.stream eq 3 and frame.len > 100`
    Display filter: `(ip.stream == 3) && (frame.len > 100)`
    Answer: 393

9. Remove the previous filter, how many packets in the pcap have the TCP SYN bit set to 1?

    Display filter: `tcp.flags.syn == 1`
    Answer: 16
    
10. How many packets have the TCP Reset bit set to 1?

    Display filter: `tcp.flags.reset == 1`
    Answer: 1

11. How many TCP SYN/ACKs are in the PCAP?

    Display filter: `tcp.flags.syn == 1 and tcp.flags.ack == 1`
    Answer: 8

Nice work! If you didn't get them all that is ok, learning process is more important than arriving at the answer at this point. 

Not for reuse or redistribution.
Property of Packet Pioneer LLC
www.packetpioneer.com