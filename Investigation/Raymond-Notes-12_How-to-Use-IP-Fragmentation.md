# IP Fragmentation Troubleshooting Cheat Sheet

## 🚨 When to Suspect IP Fragmentation Issues

### Real-World Troubleshooting Scenarios

1. **Performance Degradation**
   - Applications running slower than expected
   - Large file transfers timing out
   - VoIP quality issues with large packets

2. **Connectivity Problems**
   - Intermittent connection drops
   - Large packets failing while small ones succeed
   - Applications working locally but failing over WAN

3. **Error Messages**
   - "Destination Unreachable - Fragmentation Needed" ICMP messages
   - Applications reporting MTU discovery failures
   - TCP connection resets during large data transfers

4. **Network Topology Changes**
   - New WAN links with different MTU sizes
   - VPN tunnels reducing effective MTU
   - MPLS networks with varying MTU configurations

## 🔍 How IP Fragmentation Works

### The Process
1. **Originating Host** sends large packet (e.g., 1500 bytes)
2. **Intermediate Router** encounters smaller MTU on next hop
3. **Router Decision**:
   - If "Don't Fragment" bit is SET → Drop packet + send ICMP error
   - If "Don't Fragment" bit is CLEAR → Fragment the packet
4. **Fragmentation** occurs: Split into smaller packets
5. **Destination Host** reassembles fragments

### MTU Examples
- **Ethernet**: 1500 bytes (most common)
- **PPPoE**: 1492 bytes
- **VPN Tunnels**: 1436-1460 bytes
- **Some WANs**: 1200 bytes or less

## 🎯 Identifying Related Fragmented Packets in Wireshark

### Key Fields to Match Fragments

#### 1. **IP Identification (IP ID)** - Primary Identifier
```
Filter: ip.id == [value]
Example: ip.id == 52444
```
- **All fragments of the same original packet have IDENTICAL IP ID**
- This is the most reliable way to group fragments

#### 2. **Source and Destination IPs**
```
Filter: ip.src == [source] and ip.dst == [destination]
```
- Fragments will have same source/destination pair

#### 3. **IP Flags Analysis**
```
Filter: ip.flags.mf == 1  (More Fragments bit set)
Filter: ip.flags.df == 0  (Don't Fragment bit clear)
```

#### 4. **Fragment Offset**
```
Filter: ip.frag_offset != 0  (Not the first fragment)
```

### Visual Indicators in Wireshark

#### Wireshark Fragmentation Markers
- **Dots in packet list**: Connected dots show related fragments
- **"Fragmented IP protocol"** in Protocol column
- **Reassembled packet info** in packet details pane

#### Fragment Sequence Recognition
```
Packet 1: [More Fragments=1, Offset=0]     ← First fragment
Packet 2: [More Fragments=0, Offset=1480]  ← Last fragment
```

## 🛠️ Wireshark Analysis Techniques

### Essential Filters for Fragmentation Troubleshooting

```bash
# Show all fragmented packets
ip.flags.mf == 1 or ip.frag_offset > 0

# Show packets that could be fragmented (DF bit clear)
ip.flags.df == 0

# Show ICMP fragmentation needed messages
icmp.type == 3 and icmp.code == 4

# Show large packets that might cause fragmentation
ip.len > 1500

# Find packets with specific IP ID
ip.id == [specific_value]

# Show only first fragments
ip.frag_offset == 0 and ip.flags.mf == 1

# Show only last fragments  
ip.frag_offset > 0 and ip.flags.mf == 0
```

### Step-by-Step Fragment Analysis

1. **Identify First Fragment**
   - Look for: `More Fragments = 1` AND `Fragment Offset = 0`
   - Note the IP ID value
   - Check total length and payload size

2. **Find Related Fragments**
   - Filter by same IP ID: `ip.id == [noted_value]`
   - Verify same src/dst IP addresses
   - Check fragment offsets are sequential

3. **Analyze Fragment Chain**
   - **First fragment**: Contains original headers (TCP/UDP)
   - **Middle fragments**: Only IP header + data
   - **Last fragment**: `More Fragments = 0`

4. **Calculate Payload Distribution**
   ```
   Fragment 1: Offset 0, Length 1500 (Payload: 0-1479)
   Fragment 2: Offset 1480, Length 162 (Payload: 1480-1607)
   ```

## ⚠️ Common Fragmentation Problems

### Performance Issues
- **CPU Overhead**: Fragmentation/reassembly is CPU intensive
- **Buffer Requirements**: Destination must buffer incomplete fragments
- **Packet Loss**: If ANY fragment is lost, entire packet is dropped
- **Out-of-Order**: Fragments may arrive out of sequence

### Troubleshooting Red Flags
- **Missing Fragments**: Incomplete reassembly
- **Duplicate IP IDs**: Same ID used for different packets
- **Fragment Timeout**: Reassembly timer expires
- **Buffer Overflow**: Too many incomplete fragments

## 🔧 Practical Troubleshooting Commands

### Generate Test Fragmentation (for testing)
```bash
# Linux - Force fragmentation with large ping
ping -s 2000 [destination_ip]

# Windows - Large ping
ping -l 2000 [destination_ip]

# Test MTU discovery
ping -f -l 1472 [destination_ip]  # Windows
ping -M do -s 1472 [destination_ip]  # Linux
```

### Network Configuration Checks
```bash
# Check interface MTU
ip link show [interface]        # Linux
netsh interface ipv4 show subinterfaces  # Windows

# Path MTU discovery
tracepath [destination]         # Linux
pathping [destination]          # Windows
```

## 📊 Fragmentation Analysis Checklist

### When Analyzing Fragments:
- [ ] Verify all fragments have same IP ID
- [ ] Check source and destination IPs match
- [ ] Confirm fragment offsets are logical
- [ ] Verify "More Fragments" bit progression
- [ ] Calculate total original packet size
- [ ] Look for missing fragments in sequence
- [ ] Check for fragmentation-related ICMP messages

### For Performance Issues:
- [ ] Measure fragment reassembly times
- [ ] Count total fragments per conversation
- [ ] Identify MTU bottlenecks in path
- [ ] Check for fragment loss patterns
- [ ] Analyze impact on application layer

## 🎯 Quick Reference: Fragment Fields

| Field | Location | Purpose |
|-------|----------|---------|
| **IP ID** | IP Header | Groups fragments together |
| **More Fragments (MF)** | IP Flags | 1=more coming, 0=last fragment |
| **Don't Fragment (DF)** | IP Flags | 1=don't fragment, 0=fragmentation allowed |
| **Fragment Offset** | IP Header | Position of fragment data (×8 bytes) |
| **Total Length** | IP Header | Length of this fragment |

## 💡 Best Practices

### Prevention
- Use Path MTU Discovery when possible
- Configure consistent MTUs across network path
- Implement TCP MSS clamping for better performance
- Monitor and adjust application packet sizes

### When Fragmentation is Necessary
- Ensure network devices can handle reassembly
- Monitor fragment loss rates
- Consider application-layer segmentation instead
- Document MTU constraints in network topology

---

*Note: In modern networks, fragmentation is generally avoided due to performance implications. TCP MSS (Maximum Segment Size) negotiation and Path MTU Discovery are preferred solutions.*
