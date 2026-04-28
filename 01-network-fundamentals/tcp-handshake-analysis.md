```
# TCP Three-Way Handshake Analysis

## What is it?
The TCP handshake is the process that establishes a connection between two devices before any data is transferred. It consists of exactly 3 steps.

## The 3 Steps

| Step | Flag | Direction | Meaning |
|------|------|-----------|---------|
| 1 | SYN | My PC → Server | "I want to connect, my sequence number is X" |
| 2 | SYN-ACK | Server → My PC | "Accepted, my sequence number is Y, confirmed yours" |
| 3 | ACK | My PC → Server | "Confirmed yours, connection established" |

## Real Capture Analysis

**Tool:** Wireshark  
**Filter used:** `tcp.flags.syn==1` then `ip.addr==34.149.66.154`

**Packets identified:**
- Frame 28046 → [SYN] — My PC initiates connection
- Frame 28054 → [SYN-ACK] — Server responds
- Frame 28057 → [ACK] — Connection established

**Sequence numbers verified:**
- Server ISN (raw): 1900220039
- Expected ACK: 1900220039 + 1 = 1900220040
- Actual ACK in frame 28057: 1900220040 ✓

## Key Insight
The ACK number is always the other side's sequence number + 1. This is how TCP confirms that both sides received each other's data. An attacker with a spoofed IP can never complete this because they never receive the SYN-ACK.

## Connection to Cadastral Security
A SYN Flood attack against a cadastral system would fill the server's backlog with fake connections, preventing legitimate users from accessing land registry data. Defense: SYN Cookies.
```

