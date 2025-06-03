# How IP TTL Works

## Quick Reference

### TTL Basics
- **Purpose**: Prevents routing loops by limiting packet lifetime
- **Location**: 1-byte field in IP header
- **Behavior**: Decremented by 1 at each router
- **Drop Rule**: When TTL reaches 0, packet is dropped

### Common Starting Values
| Operating System | Typical TTL |
|------------------|-------------|
| Windows          | 128         |
| Infrastructure devices | 255   |
| Linux/Unix       | 64          |

## How TTL Works

### Packet Journey
```
Client → Router1 → Router2 → Router3 → Destination
TTL:128    127      126       125        (received)
```

### Return Path
```
Destination → Router3 → Router2 → Router1 → Client
TTL: 64        63        62        61      (received)
```

**Key Point**: Each direction starts with a **fresh TTL value** - responses don't reuse the incoming TTL.

## Troubleshooting with TTL

### Distance Calculation
**Formula**: `Hops = Starting_TTL - Observed_TTL`

**Example**: Ping shows TTL=117
- Most likely started at 128 (not 64 or 255)
- Calculation: 128 - 117 = 11 hops away

### Wireshark Analysis
1. **Add TTL Column**: Right-click TTL field → Add as Column
2. **Compare Values**: 
   - Outgoing packets: Show your system's TTL
   - Incoming packets: Show remote system's path length

### Practical Examples

#### Ping Output
```
Reply from 8.8.8.8: bytes=32 time=20ms TTL=117
```
- Google likely started at TTL=128
- Traveled through ~11 routers

#### TCP Conversation
- **SYN packet**: TTL=64 (outgoing from your system)
- **SYN-ACK packet**: TTL=57 (incoming, likely started at 64)
- **Analysis**: Remote system is ~7 hops away

## Key Troubleshooting Uses

### 1. Detect Unrouted Traffic
- **Full TTL** (64, 128, 255): Packet hasn't been routed
- **Decremented TTL**: Packet has traveled through routers

### 2. Estimate Network Distance
- Compare received TTL with common starting values
- Calculate approximate hop count

### 3. Routing Loop Prevention
- TTL=1 → Router decrements to 0 → Packet dropped
- Prevents infinite loops in network

## Best Practices

1. **Remember the big 3**: 64, 128, 255
2. **TTL never increases** during transit
3. **Each direction starts fresh** - no TTL inheritance
4. **Add TTL column** in Wireshark for quick reference
5. **Use with traceroute** to validate hop count estimates

## Common Misconceptions

❌ **Wrong**: Response packets use the same TTL as the request  
✅ **Correct**: Each direction starts with its own fresh TTL

❌ **Wrong**: TTL can increase during transit  
✅ **Correct**: TTL only decreases (or stays same at destination)

❌ **Wrong**: All systems use the same starting TTL  
✅ **Correct**: Starting TTL varies by operating system
