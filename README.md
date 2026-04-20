*This project has been created as part of the 42 curriculum by migusant.*

# NetPractice

A hands-on introduction to **TCP/IP networking**: configuring IP addresses, subnet masks, routing tables, and default gateways on small simulated networks until they function correctly.

## Description

NetPractice is a browser-based training tool that presents broken network diagrams — hosts, routers, switches, and their interfaces — and asks you to fix them. Each of the **10 levels** tests a progressively deeper understanding of how addressing and routing work in a TCP/IP network.

The project does not involve writing code. Instead, it exercises the conceptual foundation of networking: recognizing which devices belong to which subnet, calculating network and broadcast addresses, assigning valid host IPs, and writing routing table entries that correctly forward traffic between segments.

### Key Concepts Exercised

- **IPv4 addressing**: converting between CIDR (`/n`) and dotted-decimal masks, identifying network and broadcast addresses, and enumerating the usable host range of a subnet.
- **Same-subnet reasoning**: determining whether two hosts can communicate directly or require a router by comparing their network portions under the given mask.
- **Default gateways**: choosing a valid gateway IP for each host from the router interface on its own segment.
- **Routing tables**: writing entries with correct destination/next-hop pairs, including the default route `0.0.0.0/0` for traffic leaving a known subnet.
- **Switch vs. router behavior**: understanding that switches operate at Layer 2 (same subnet, MAC-based) while routers operate at Layer 3 (between subnets, IP-based).
- **Reserved addresses**: avoiding the network address, broadcast address, and other non-assignable ranges when filling in interface IPs.

### Repository Structure

```
├── level01.nptf          # Exported configuration for level 1
├── level02.nptf          # Exported configuration for level 2
├── level03.nptf          (...)
├── level04.nptf
├── level05.nptf
├── level06.nptf
├── level07.nptf
├── level08.nptf
├── level09.nptf
├── level10.nptf
├── NetPractice_Notes.md  # Personal study notes on addressing and subnetting
└── README.md
```

## Instructions

### Running the Training Interface

The training interface is provided as a static web page served locally. From the project archive (downloaded from the 42 intranet):

```bash
./run.sh
```

This launches a local web server and opens the interface in your default browser. If `run.sh` does not work in your environment, start the server manually:

```bash
python3 -m http.server 49242
```

Then navigate to `http://localhost:49242` in your browser.

### Solving and Exporting Each Level

1. In the interface, enter your intranet login (**required** — the evaluation is tied to it) and click **Start**.
2. For each level:
   - Read the goal at the top of the page (e.g. "host A needs to communicate with host B").
   - Modify the **unshaded fields** (IPs, masks, gateways, routing table entries) until the diagram turns green.
   - Use **Check again** to verify the configuration.
   - Use **Get my config** to export the current state as a `.nptf` file.
3. Place each exported file at the repository root, named `level01.nptf` through `level10.nptf`.
4. Advance to the next level using the button that appears after a successful check.

### Submission Requirements

- **10 configuration files** must be placed at the root of the Git repository — one per level, named `level01.nptf` through `level10.nptf`.
- Each file must correspond to a fully solved level (all goals met, all diagnostics clean).
- This `README.md` must be present at the root.
- During the defense, three random levels will be selected and must be solved from scratch within a limited time. Only a basic calculator such as `bc` is permitted.

## Resources

### Classic References

**Core networking concepts studied:**
- [Wikipedia: Internet Protocol Suite (TCP/IP)](https://en.wikipedia.org/wiki/Internet_protocol_suite) — overall protocol family
- [Wikipedia: IPv4](https://en.wikipedia.org/wiki/IPv4) — address format, header, fragmentation
- [Wikipedia: Subnet](https://en.wikipedia.org/wiki/Subnet) — subnet masks and CIDR
- [Wikipedia: Network Address](https://en.wikipedia.org/wiki/Network_address) — the reserved "first" address of a subnet
- [Wikipedia: Broadcast Address](https://en.wikipedia.org/wiki/Broadcast_address) — the reserved "last" address of a subnet
- [Wikipedia: Default Gateway](https://en.wikipedia.org/wiki/Default_gateway) — how hosts reach foreign subnets
- [Wikipedia: Routing](https://en.wikipedia.org/wiki/Routing) — how routers forward packets between networks
- [Wikipedia: Router (computing)](https://en.wikipedia.org/wiki/Router_(computing)) — Layer 3 forwarding device
- [Wikipedia: Network Switch](https://en.wikipedia.org/wiki/Network_switch) — Layer 2 forwarding device
- [Wikipedia: OSI Model](https://en.wikipedia.org/wiki/OSI_model) — seven-layer reference model
- [Wikipedia: Private Network](https://en.wikipedia.org/wiki/Private_network) — RFC 1918 address ranges
- [Wikipedia: List of Reserved IP Addresses](https://en.wikipedia.org/wiki/List_of_reserved_IP_addresses) — ranges that must not appear as host IPs
- [Wikipedia: Classful Network](https://en.wikipedia.org/wiki/Classful_network) — the historical class A/B/C scheme and why CIDR replaced it
- [Wikipedia: Network Address Translation](https://en.wikipedia.org/wiki/Network_address_translation) — how private networks reach the internet
- [Wikipedia: Localhost](https://en.wikipedia.org/wiki/Localhost) and [Loopback](https://en.wikipedia.org/wiki/Loopback) — the `127.0.0.0/8` range

**TCP/IP layer references:**
- [Wikipedia: Link Layer](https://en.wikipedia.org/wiki/Link_layer) — frames, MAC addresses (Ethernet, Wi-Fi)
- [Wikipedia: Internet Layer](https://en.wikipedia.org/wiki/Internet_layer) — IP addressing and routing
- [Wikipedia: Transport Layer](https://en.wikipedia.org/wiki/Transport_layer) — TCP and UDP
- [Wikipedia: Application Layer](https://en.wikipedia.org/wiki/Application_layer) — HTTP, DNS, SSH, etc.

**Subnetting practice:**
- [Practical Networking — Subnetting Mastery playlist (YouTube)](https://youtube.com/playlist?list=PLIFyRwBY_4bQUE4IB5c4VPRyDoLgOdExE) — 7-part series on the "seven attributes" method and cheat-sheet technique.
- [SubnetIPv4.com](https://subnetipv4.com) — companion practice site with random subnetting problems.

**Personal study notes:**
- [`NetPractice_Notes.md`](./NetPractice_Notes.md) — condensed reference compiled while working through the project. Covers the cheat-sheet method, worked examples, common caveats (target on a boundary, octet rollover), speed tips, and the surrounding protocol/layer context.

### AI Usage

AI tools (GitHub Copilot, ChatGPT) were used as a **study and documentation partner**, not to solve the levels. Each level was solved manually by reasoning about subnets, gateways, and routing tables.

**Study material preparation:**
- Condensing the Wikipedia references listed above into the study notes file (`NetPractice_Notes.md`), including the cheat-sheet tables, worked examples, and caveat sections.
- Cross-checking the summarized concepts against the simulator's behavior to catch oversimplifications.

**Documentation:**
- Structuring and refining this `README.md` to meet the subject's required sections (Description, Instructions, Resources, submission details) and to use consistent networking vocabulary.

**Concept clarification:**
- Explaining the distinction between switches (Layer 2) and routers (Layer 3) when a level's diagram was ambiguous.
- Clarifying why certain gateway or next-hop configurations produced specific error messages in the simulator's log panel.

## License

This project is part of the 42 Common Core curriculum. See [LICENSE](LICENSE) for details.