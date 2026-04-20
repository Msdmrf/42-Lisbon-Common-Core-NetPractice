*This document has been created as a study guide by migusant.*

# NetPractice — Study Notes

Concise reference for the 10 levels and the defense. Covers what the simulator actually tests: IP addressing, subnet masks, routing, gateways — plus the surrounding concepts you should be able to explain.

## 1. IP Addresses

An IPv4 address is 32 bits written as four octets, each `0–255`: `192.168.1.10`.

Split into two parts by the subnet mask:
- **Network part** — identifies the subnet (shared by all hosts on that link).
- **Host part** — identifies the specific device.

Hosts on the same segment must share the same network part, or they can't talk directly.

## 2. Subnet Masks

A mask is 32 bits: a run of `1`s (network bits) followed by `0`s (host bits).

| CIDR | Dotted decimal    | Usable hosts |
|------|-------------------|--------------|
| /8   | 255.0.0.0         | 16,777,214   |
| /16  | 255.255.0.0       | 65,534       |
| /24  | 255.255.255.0     | 254          |
| /25  | 255.255.255.128   | 126          |
| /26  | 255.255.255.192   | 62           |
| /27  | 255.255.255.224   | 30           |
| /28  | 255.255.255.240   | 14           |
| /29  | 255.255.255.248   | 6            |
| /30  | 255.255.255.252   | 2            |

Usable hosts = `2^(host bits) − 2` (two reserved — the Network ID and Broadcast, see §3).

For a fast method to convert CIDR ↔ mask and compute subnet boundaries, see §3.

## 3. The Seven Attributes Method

Credit: [Practical Networking — Subnetting Mastery playlist](https://youtube.com/playlist?list=PLIFyRwBY_4bQUE4IB5c4VPRyDoLgOdExE&si=5dTrHlEZk6JOsw_7).

Every subnetting problem boils down to seven fields. Learn to fill them all and you've mastered the topic.

| Attribute | Definition |
|---|---|
| **Network ID** | First IP in the subnet (host bits all `0`) |
| **Broadcast IP** | Last IP in the subnet (host bits all `1`) |
| **First Host** | IP *after* the Network ID |
| **Last Host** | IP *before* the Broadcast IP |
| **Next Network** | IP *after* the Broadcast IP (= Network ID of the next subnet) |
| **# IP Addresses** | Total IPs in the subnet (= Group Size). Subtract 2 for usable. |
| **CIDR / Subnet Mask** | Convert between `/n` and dotted-decimal |

Network ID and Broadcast are the two reserved addresses — they can't be assigned to hosts.

### The Cheat Sheet

Draw this by hand before each defense. Three rows, eight columns:

| Group Size  | 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |
|-------------|-----|-----|-----|-----|-----|-----|-----|-----|
| Subnet Mask | 128 | 192 | 224 | 240 | 248 | 252 | 254 | 255 |
| CIDR        | /25 | /26 | /27 | /28 | /29 | /30 | /31 | /32 |

**How to build it from scratch:**
1. Row 1 (Group Size): start at `1` on the right and double leftward until `128`.
2. Row 2 (Subnet Mask): each entry = `256 − (Group Size above it)`.
3. Row 3 (CIDR): start at `/32` on the right and count down to `/25`.

### Extending to Higher Octets

The strip above covers `/25`–`/32`. For `/17`–`/24`, add a fourth row that shows which CIDR lines up with each column — the Group Size and Subnet Mask values stay the same; only the position of the relevant octet shifts left.

| Group Size          | 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |
|---------------------|-----|-----|-----|-----|-----|-----|-----|-----|
| Subnet Mask         | 128 | 192 | 224 | 240 | 248 | 252 | 254 | 255 |
| CIDR (4th octet)    | /25 | /26 | /27 | /28 | /29 | /30 | /31 | /32 |
| CIDR (3rd octet)    | /17 | /18 | /19 | /20 | /21 | /22 | /23 | /24 |
| CIDR (2nd octet)    | /9  | /10 | /11 | /12 | /13 | /14 | /15 | /16 |
| CIDR (1st octet)    | /1  | /2  | /3  | /4  | /5  | /6  | /7  | /8  |

Same column → same Group Size and Subnet Mask value, regardless of octet. The position of the relevant octet just shifts left as the CIDR gets smaller.

**Rules when the relevant octet isn't the 4th:**
- Apply the method to the **relevant octet** (e.g. 3rd for `/17`–`/24`). Octets to the *left* of it stay as-is.
- Octets to the *right* of the relevant octet: `0` in the Network ID, `255` in the Broadcast.
- **Next Network** can roll into the octet to the left. E.g. `10.4.192.0 /18` → Next Network `10.5.0.0`.
- **# IPs** = `2^(32 − CIDR)`. For `/19` that's `2^13 = 8192`.
- Subnet Mask is derived by the column: `/19` → column with Subnet `224` → full mask `255.255.224.0`.

**Worked example: `10.4.77.188 /19`**

`/19` lands in the 3rd octet. Group Size `32`. Target's 3rd octet: `.77`. Boundaries in the 3rd octet: `.0, .32, .64, .96, …`. `.77` sits between `.64` and `.96`.

| Attribute | Value |
|---|---|
| Network ID | `10.4.64.0` |
| Broadcast | `10.4.95.255` |
| First Host | `10.4.64.1` |
| Last Host | `10.4.95.254` |
| Next Network | `10.4.96.0` |
| # IPs | 8,192 (8,190 usable) |
| Subnet Mask | `255.255.224.0` |

### Solving Any Subnetting Problem in Seconds

Given an IP + CIDR (e.g. `10.1.1.55 /28`):

1. **Find the interesting octet** — the one the CIDR lands in. For `/25`–`/32` it's the 4th; `/17`–`/24` the 3rd; and so on.
2. **Look up Group Size** from the cheat sheet. For `/28` → Group Size `16`.
3. **List the subnet boundaries** in the interesting octet by counting up by Group Size: `.0, .16, .32, .48, .64, .80, .96, .112, .128, …`
4. **Network ID** = the boundary at or below your IP's interesting octet. `.55` sits between `.48` and `.64`, so Network ID = `10.1.1.48`.
5. **Broadcast** = Network ID + (Group Size − 1) in the interesting octet → `10.1.1.63`.
6. **First Host** = Network ID + 1 → `10.1.1.49`.
7. **Last Host** = Broadcast − 1 → `10.1.1.62`.
8. **Next Network** = Network ID + Group Size → `10.1.1.64`.
9. **# IPs** = Group Size = `16` (14 usable).
10. **Subnet Mask** = look up on the cheat sheet → `255.255.255.240`.

### Worked Example: `10.1.1.37 /29`

Interesting octet: 4th. Group Size for `/29` = `8`. Boundaries: `.0, .8, .16, .24, .32, .40, .48, …`. `.37` sits between `.32` and `.40`.

| Attribute | Value |
|---|---|
| Network ID | `10.1.1.32` |
| Broadcast | `10.1.1.39` |
| First Host | `10.1.1.33` |
| Last Host | `10.1.1.38` |
| Next Network | `10.1.1.40` |
| # IPs | 8 (6 usable) |
| Subnet Mask | `255.255.255.248` |

### Caveat 1 — When the Target IP Is Itself a Boundary: `10.2.2.20 /30`

Group Size for `/30` = `4`. Boundaries: `.0, .4, .8, .12, .16, .20, .24, …`. The target `.20` is already on a boundary.

**Rule:** always keep counting until you *pass* the target. Stop at `.24`. The boundary *before* that (which is `.20` itself) is the Network ID.

| Attribute | Value |
|---|---|
| Network ID | `10.2.2.20` |
| Broadcast | `10.2.2.23` |
| First Host | `10.2.2.21` |
| Last Host | `10.2.2.22` |
| Next Network | `10.2.2.24` |
| # IPs | 4 (2 usable) |
| Subnet Mask | `255.255.255.252` |

A `/30` has only 2 usable hosts — the typical choice for router-to-router point-to-point links.

### Caveat 2 — When Next Network Crosses an Octet Boundary: `10.2.2.199 /26`

Group Size for `/26` = `64`. Boundaries in the 4th octet: `.0, .64, .128, .192`. `.199` sits between `.192` and the next boundary — but counting up by 64 from `.192` gives `.256`, which isn't a valid octet value.

**Rule:** when the next step would exceed `255`, the boundary rolls into the *next* octet. `.192 + 64` → `10.2.3.0`.

| Attribute | Value |
|---|---|
| Network ID | `10.2.2.192` |
| Broadcast | `10.2.2.255` |
| First Host | `10.2.2.193` |
| Last Host | `10.2.2.254` |
| Next Network | `10.2.3.0` |
| # IPs | 64 (62 usable) |
| Subnet Mask | `255.255.255.192` |

This happens whenever the current subnet is the last one that fits inside an octet. Watch for it around `.192/26`, `.224/27`, `.240/28`, etc.

**Edge case — no Next Network at all.** When the Next Network would exceed `255.255.255.255` entirely (the last subnet of the whole address space), there is no next network. Mark it `n/a`. This only happens for subnets anchored at the very top of the space — e.g. `192.0.0.0 /2` has Broadcast `255.255.255.255` and no Next Network. Not something NetPractice is likely to test, but worth knowing.

### Speed Tips

Counting `.0, .8, .16, .24, …` all the way up to a target like `.170` is slow. Two shortcuts let you skip most of the counting.

**Tip A — Jump-start from a known boundary.** Any value in the **Subnet Mask** row of the cheat sheet (128, 192, 224, 240, 248, 252, 254, 255) is automatically a valid boundary for any *equal or smaller* Group Size. So you can start counting from the closest cheat-sheet value instead of from `.0`.

Concrete consequences:
- `.128` is always a boundary — every Group Size from 1 through 128 divides it evenly.
- `.192` is a boundary for Group Sizes 1, 2, 4, 8, 16, 32, 64.
- `.224` is a boundary for Group Sizes 1, 2, 4, 8, 16, 32.
- And so on across the Subnet row.

*Example (`10.3.3.117 /29`):* Group Size 8. Instead of counting `0, 8, 16, … 112`, jump to `.128` (known boundary) and count *down* by 8: `120, 112`. Target `.117` sits between `.112` and `.120` → Network ID `10.3.3.112`.

**Tip B — ×10, then double or triple.** For large octet values, multiplying the Group Size by 10 gets you into the right ballpark fast.

*Example (`10.3.3.170 /29`):* Group Size 8. `8 × 10 = 80`, double → `160`, add one more Group Size → `168, 176`. Target `.170` sits between `.168` and `.176` → Network ID `10.3.3.168`.

Combine both: when the target is in the upper half of an octet, jump to `.128` and count forward; when it's in the lower half, count from `.0` or use ×10. Either way, you should never need more than 3–4 boundaries listed out.

## 4. Same-Subnet Check

Two IPs are on the same subnet iff applying the mask gives the same network address.

**Example:** `192.168.1.50/26` vs `192.168.1.100/26`. Group Size 64 → boundaries `.0, .64, .128, .192`. `.50` is in `.0–.63`; `.100` is in `.64–.127`. Different Network IDs — they need a router.

This is the single most common check in NetPractice.

## 5. Default Gateway

A host reaches only its own subnet directly. For anything else, it forwards to its **default gateway** — a router interface on the host's own subnet.

- Gateway must be in the host's subnet.
- Gateway can't be the host's own IP, the network address, or the broadcast.
- Usually the gateway is the router's interface IP on that LAN.

"Destination does not match any route" usually means missing or wrong gateway.

## 6. Switches vs Routers

- **Switch** — Layer 2. Forwards Ethernet frames within one subnet by MAC address. No IP of its own. Everything plugged into a switch is one subnet.
- **Router** — Layer 3. Each interface has its own IP on a different subnet. Forwards between subnets using its routing table. The interface IP on a given LAN is the gateway for hosts there.

## 7. Routing Tables

Each entry has a **destination** and a **next hop** (`=>`).

- `0.0.0.0/0` = default route (matches anything not covered by a more specific entry).
- Next hop must be on a **directly connected** subnet — you can't point at a distant router.
- Directly connected subnets are handled automatically; you only configure routes for non-attached subnets.

**Host:** `0.0.0.0/0 => <router interface on my LAN>`
**Router uplink:** `0.0.0.0/0 => <next router up the chain>`

Router-to-router links often use a `/30` subnet (exactly 2 usable IPs — one per interface).

## 8. Private vs Public Addresses

Not every IPv4 address is routable on the open internet. Several ranges are carved out for special purposes. The ones below are the ones you're most likely to see or be asked about.

**Private ranges (RFC 1918)** — routable only inside private networks. These are the three you'll see constantly:
- `10.0.0.0/8` — single class-A block
- `172.16.0.0/12` — covers `172.16.0.0` through `172.31.255.255`
- `192.168.0.0/16` — covers 256 class-C-sized blocks, most common in home networks

**Loopback and link-local** — not "private" in the RFC 1918 sense but also not routable:
- `127.0.0.0/8` — loopback. Anything you send here comes back to you. `127.0.0.1` is the classic "localhost".
- `169.254.0.0/16` — link-local / APIPA. What your OS self-assigns when DHCP fails.

**Other reserved ranges ("forbidden" for normal host use):**
- `0.0.0.0/8` — "this network". `0.0.0.0` itself means "any address" (used in routing tables as the default route).
- `100.64.0.0/10` — Carrier-Grade NAT (shared address space used by ISPs internally).
- `192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24` — TEST-NET-1/2/3, reserved for documentation and examples.
- `224.0.0.0/4` — multicast (old class D). Not assignable to hosts.
- `240.0.0.0/4` — reserved "for future use" (old class E). Effectively forbidden — most stacks refuse to route it.
- `255.255.255.255/32` — limited broadcast (the entire local network).

**What this means for NetPractice:** the simulator mixes private and public-looking addresses freely and doesn't care which is which. But if a level uses `0.0.0.0`, `127.0.0.1`, or `255.255.255.255` as an interface IP, that's a bug in your configuration — those addresses can never be assigned to a host.

## 9. Solving Method

1. **Identify LAN segments.** A switch (or direct link) bounds one segment; each router interface is a boundary.
2. **Pick the subnet per segment** — one pre-filled interface or host fixes it.
3. **Assign interface IPs** in that subnet; avoid network address, broadcast, and duplicates.
4. **Set each host's gateway** to the router interface on its segment.
5. **Fill routing tables** so every router can reach every subnet. Default route covers most cases.
6. **Read the logs** — they name the exact failure.

## 10. Common Log Messages

| Message | Meaning |
|---|---|
| `destination does not match any interface` | Will route via table — check routes next |
| `destination does not match any route` | No route/default gateway covers the destination |
| `correct IP reached but wrong host` | IP clash or wrong subnet partitioning |
| `gateway is not on the same network as interface` | Gateway IP isn't in the host's subnet |
| `invalid IP` / `invalid mask` | Out-of-range address, or non-contiguous mask |

## 11. Protocols and Network Models

### Why "TCP/IP"?

**IP** delivers individual packets across interconnected networks — connectionless, best-effort, no guarantees. **TCP** runs on top of IP and adds reliability, ordering, and flow control via sequence numbers, acks, and retransmission. They were designed together; every TCP segment is wrapped in an IP packet. The name **TCP/IP** now refers to the whole protocol family that grew around them.

NetPractice only tests the IP layer — TCP never appears in the simulator.

### Layers and Encapsulation

Networking is split into layers, each with one job. Every layer wraps the data above with its own header:

```
[ Ethernet [ IP [ TCP [ HTTP data ] ] ] ]
```

This lets each layer change independently — swap Wi-Fi for Ethernet without touching the browser.

### TCP/IP Model (4 Layers — the Practical One)

| Layer | Job | Addresses | Key protocols |
|---|---|---|---|
| **Application** | What the program does | hostnames, URLs | HTTP(S), DNS, SSH, FTP, SMTP, DHCP |
| **Transport** | End-to-end program-to-program delivery | ports | **TCP**, **UDP**, QUIC |
| **Internet** | Packet routing between networks | IP addresses | **IP**, ICMP, routing protocols (OSPF, BGP) |
| **Link** | Frames on one physical link | MAC addresses | Ethernet, Wi-Fi, ARP |

Key non-TCP protocols to know:
- **UDP** — no reliability, no ordering, fast. Used by DNS, video, games.
- **ICMP** — diagnostics. `ping` is ICMP Echo. "Destination unreachable" is ICMP.
- **ARP** — maps IP → MAC on a local network (every LAN packet needs this first).
- **DHCP** — auto-assigns IP, mask, gateway (the opposite of what you do manually in NetPractice).
- **DNS** — maps hostnames → IP addresses.

### OSI Model (7 Layers — the Academic One)

Formal ISO reference. Never deployed as-is, but its layer *numbers* are still used colloquially ("Layer 2 switch", "Layer 7 firewall").

1. Physical — cables, voltages, radio
2. Data Link — framing, MAC (Ethernet, Wi-Fi)
3. Network — logical addressing, routing (IP)
4. Transport — end-to-end delivery (TCP, UDP)
5. Session — managing conversations
6. Presentation — encoding, encryption (TLS)
7. Application — the program itself

### Mapping OSI ↔ TCP/IP

```
OSI                           TCP/IP
─────────────────             ───────────
7 Application  ┐
6 Presentation ├──────────>   Application
5 Session      ┘
4 Transport    ───────────>   Transport
3 Network      ───────────>   Internet
2 Data Link    ┐
1 Physical     ┴──────────>   Link
```

OSI splits the top into three layers and the bottom into two; TCP/IP lumps each end together. A common textbook compromise is a **5-layer hybrid**: Application, Transport, Network, Data Link, Physical.

### What This Means for NetPractice

Everything you configure is **Internet layer** (OSI 3): IPs, masks, gateways, routes. Switches work at the **Link layer** (OSI 2), which is why they have no IPs. Routers bridge the two — Layer 3 IPs on each interface, Layer 2 framing on each wire.