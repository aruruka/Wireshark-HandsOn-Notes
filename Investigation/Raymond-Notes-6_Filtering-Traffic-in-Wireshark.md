# Filtering Traffic in Wireshark

## Capture filter and display filter

![[Wireshark_Capture-Filter-and-Display-Filter.jpg]]

1. **Capture Filter Definition**:
   - Emphasize it's applied during packet capture using BPF syntax
   - Acts as first-line filtering to reduce resource usage
   - Example: `tcp port 80` to capture only web traffic

2. **Display Filter Definition**:
   - Applied post-capture to analyzed packets
   - Uses Wireshark's rich filtering syntax
   - Example: `http.request.method == "GET"` to show only GET requests

3. **Key Relationship**:
   - Sequential application (capture first, then display)
   - Capture filters limit what's stored, display filters limit what's shown
   - Complementary roles in traffic analysis workflow

4. **Syntactic Differences**:
   - Capture filters use tshark-style syntax
   - Display filters use Wireshark's enhanced syntax

5. **Practical Implications**:
   - Capture filters are irreversible (discarded packets lost forever)
   - Display filters preserve original capture data


**Final Explanation**:

Capture filters and display filters serve distinct but complementary roles in Wireshark traffic analysis. A capture filter (using BPF syntax like `udp port 53`) acts at the collection phase, determining which packets enter Wireshark's buffer - this conserves system resources by excluding unwanted traffic upfront. The display filter (using Wireshark's enhanced syntax like `dns.qry.name contains "example.com"`) operates post-capture, refining visibility of already captured packets without altering the stored data. Their relationship is sequential: capture filters define the initial dataset while display filters enable focused analysis. This dual-layer approach allows network professionals to balance capture efficiency with detailed investigative flexibility.

## Take a look at the capture view in Wireshark

☸ Wheel icon in the main toolbar > Wireshark Capture Options > Capture filter expression inbox at the bottom

### Some high level capture filter examples

- `host 192.0.2.1` - Capture traffic to/from a specific host
- `tcp port 80` - Capture only HTTP traffic

**Suggestions**:

- Keep capture filters very simple
- Keep them very limited

Because a lot of time you will only have one chance to capture the traffic.

Let me give you an example of something that actually happened.

One example I like to use involves security cameras around your house. 

If those cameras aren't running when a thief breaks in, you'll miss the event entirely. 
It doesn't matter if you later start up those cameras and architect a perfect system 
with long-term storage and all the features - if it wasn't running when the incident occurred, 
you won't have the data. You'll have missed capturing the event.

The same principle applies to packet capture. If an event happens and we weren't capturing, 
or if we used a capture filter that was looking for the wrong thing, we might not get 
a second chance to capture that data.

As a personal habit in my analysis work, I do use capture filters, but I employ them 
quite sparingly.

## Common display filter

| Filter Type         | Display Filter         |
| :------------------ | :--------------------- |
| IPv4 Address        | `ip.addr==10.0.0.1`    |
| IPv4 Source         | `ip.src==10.0.0.1`     |
| IPv4 Range (Subnet) | `ip.addr==10.0.0.0/24` |
| TCP Port            | `tcp.port==80`         |
| TCP SYNs            | `tcp.flags.syn==1`     |

**Some GUI common operations**

- A great way to set a filter is: **Select one packet you are interested in > right click > Conversation Filter > IPv4**
- Follow > TCP Stream ❌
- Conversation > TCP   ✔
### Common operators used in network filters

Both symbolic and textual representations are available.

The operators are shown in two forms: symbolic representation and their corresponding textual equivalent:

* `==` (equals) : `eq` (equals)
* `!` (not) : `not`
* `||` (logical OR) : `or`
* `&&` (logical AND) : `and`
* `>` (greater than) : `gt` (greater than)
* `<` (less than) : `lt` (less than)

An example filter expression is provided:
`ip.addr eq 192.168.1.1 && tcp`

This example demonstrates how to combine an IP address equality check with a logical AND to filter for TCP traffic to or from the IP address 192.168.1.1.

### Special filters

There are three advanced filtering methods for network analysis: `contains` for exact string matching, `matches` for regular expression pattern matching, and `in {range}` for specifying a set or range of values.

These methods are commonly used in network analysis tools like Wireshark, to refine packet inspection:

* **contains (exact string)**
    This filter is used to find packets that contain a specific, exact string within a designated field.
    * **Example:** `frame contains "google"`
        This expression would capture any frame that contains the exact string "google" anywhere within its data payload.

* **matches (regex)**
    This filter allows for more flexible pattern matching using regular expressions (regex).
    * **Example:** `http.host matches "\\.(org|com|net)"`
        This expression would filter for HTTP requests where the host field ends with `.org`, `.com`, or `.net`. The `\\.` escapes the dot, indicating a literal dot, and `(org|com|net)` uses a logical OR for the specified top-level domains.

* **in {range}**
    This filter is used to specify a set of discrete values or a range of values for a particular field.
    * **Example:** `tcp.port in {80,443,8000..8004}`
        This expression would filter for TCP packets where the destination or source port is 80, 443, or any port from 8000 through 8004 (inclusive).

### Targeted objects commonly being filtered

- Filtering for IP addresses
- Filtering for ports and conversations
- Filtering for Strings. Maybe passwords?
