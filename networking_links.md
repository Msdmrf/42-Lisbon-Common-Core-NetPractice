*This document has been created as a study guide by migusant, pde-alme.*

# Networking Study Notes (Simple → Complex)

A structured set of references to learn networking from fundamentals up through real-world Internet routing and infrastructure.

---

## 1) Models & Mental Framework (start here)
Learn *why* networking is layered before learning specific protocols.

- [OSI Model and Layering](https://en.wikipedia.org/wiki/OSI_model)
- [TCP/IP - Internet Protocol Suite](https://en.wikipedia.org/wiki/Internet_protocol_suite)

---

## 2) TCP/IP Layers (what each layer is responsible for)
Get a high-level understanding of each layer’s purpose and what “belongs” there.

- [TCP/IP - Link Layer](https://en.wikipedia.org/wiki/Link_layer)
- [TCP/IP - Internet Layer](https://en.wikipedia.org/wiki/Internet_layer)
- [TCP/IP - Transport Layer](https://en.wikipedia.org/wiki/Transport_layer)
- [TCP/IP - Application Layer](https://en.wikipedia.org/wiki/Application_layer)

---

## 3) Core Network Building Blocks (devices + endpoints)
Understand the basic components you’ll see in LANs and home/office networks.

- [Interfaces (Network Interface Controller)](https://en.wikipedia.org/wiki/Network_interface_controller)
- [Switches](https://en.wikipedia.org/wiki/Network_switch)
- [Routers](https://en.wikipedia.org/wiki/Router_(computing))
- [Local Area Network (LAN)](https://en.wikipedia.org/wiki/Local_area_network)
- [Wide Area Network (WAN)](https://en.wikipedia.org/wiki/Wide_area_network)

---

## 4) IP Addressing Basics (what an IP address is)
Learn what addresses represent before diving into IPv4/IPv6 details.

- [IP Address](https://en.wikipedia.org/wiki/IP_address)

---

## 5) IPv4 then IPv6 (address formats and core ideas)
Start with IPv4 because it’s still extremely common, then move to IPv6.

- [TCP/IP - IPv4 Addressing](https://en.wikipedia.org/wiki/IPv4)
- [TCP/IP - IPv6 Addressing](https://en.wikipedia.org/wiki/IPv6)

---

## 6) “Special” Addresses You Must Recognize
These show up constantly in troubleshooting and network design.

- [Private Networks](https://en.wikipedia.org/wiki/Private_network)
- [Reserved IP Addresses](https://en.wikipedia.org/wiki/List_of_reserved_IP_addresses)
- [Localhost](https://en.wikipedia.org/wiki/Localhost)
- [Loopback](https://en.wikipedia.org/wiki/Loopback)

> Note: “localhost” is a name (commonly mapped to loopback), while “loopback” is the addressing/behavior concept.

---

## 7) Subnetting & Address Types (how networks are divided)
This is where addressing becomes “operational”: calculating boundaries and ranges.

- [Subnets and Subnet Masks](https://en.wikipedia.org/wiki/Subnet)
- [The Network Address](https://en.wikipedia.org/wiki/Network_address)
- [The Broadcast Address](https://en.wikipedia.org/wiki/Broadcast_address)

---

## 8) Local Routing Concepts (how traffic leaves your subnet)
These concepts connect your subnet to other networks.

- [Default Gateway](https://en.wikipedia.org/wiki/Default_gateway)
- [Routing](https://en.wikipedia.org/wiki/Routing)

---

## 9) NAT (common real-world networking behavior)
Understand how many networks share public IP space in practice (especially IPv4).

- [Network Address Translation (NAT)](https://en.wikipedia.org/wiki/Network_address_translation)

---

## 10) The Internet at Scale (how the global network is organized)
Zoom out from small networks to backbone infrastructure and “core” routing.

- [Internet Backbone](https://en.wikipedia.org/wiki/Internet_backbone)
- [Core Routers/Networks](https://en.wikipedia.org/wiki/Core_router)

---

## 11) Address Allocation & Governance (who distributes IP space)
This is more “how the Internet is managed” than “how packets flow”, but it’s useful context.

- [Regional Internet Registry (RIR)](https://en.wikipedia.org/wiki/Regional_Internet_registry)

---

## Appendix: Historical / Legacy Concepts (optional)
Read this after you understand subnetting and CIDR-style thinking.

- [Network Classes (Classful Networking)](https://en.wikipedia.org/wiki/Classful_network)