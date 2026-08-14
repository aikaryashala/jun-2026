# How a Packet Finds You — Answer Key

Check the **reasoning**. Networking questions are easy to answer with a memorised word and no understanding; the test is whether you can say *why* a packet went where it went.

Everything here comes from six ideas: an address is **four bytes**; `192.168.x.x` is **private and unreachable from outside**; the **mask** decides direct-or-gateway; **DHCP** leases the address; **NAT** remembers connections by **port**, and only outbound ones; and **geography is not topology**.

---

# Part A — Multiple Choice

**A1 — C) 2^32.** Four bytes, 32 bits, `256^4` = 4,294,967,296. Straight from Task 36.

**A2 — B) a private address, usable only inside a local network.** `192.168.0.0/16` is one of the three RFC 1918 ranges. Millions of machines worldwide hold this exact address right now, which is only workable because none of them is reachable from outside.

**A3 — D) `172.40.0.0/16`.** The private block is `172.16.0.0/12`, which covers `172.16` to `172.31` only. `172.40` is outside it and is a perfectly ordinary public address — a favourite trap, because it *looks* private.

**A4 — B) DHCP failed and the machine assigned itself an address.** `169.254.0.0/16` is link-local. Seeing it means no DHCP server answered — treat it as "no network".

**A5 — C) the machine talking to itself.** Loopback. Packets to `127.0.0.1` never reach a cable or the air, which is why `python3 -m http.server` on localhost works with the WiFi switched off.

**A6 — B) `255.255.255.0`.** 24 one-bits followed by 8 zero-bits: `11111111 11111111 11111111 00000000`.

**A7 — C) 254.** 256 addresses minus the network address (`.0`) and the broadcast (`.255`), neither of which can be given to a machine.

**A8 — B) send it to the default gateway.** `192.168.3.50 & 255.255.255.0` = `192.168.3.0`, which differs from `192.168.0.0`, so it is not local.

**A9 — B) it ANDs both addresses with the subnet mask and compares.** The same `&` from Task 10, applied to four bytes. Not a text comparison (A) — `192.168.0.11` and `192.168.10.11` share a text prefix but are different networks.

**A10 — C) Discover, Offer, Request, Acknowledge.** DORA.

**A11 — B) the machine has no address yet, so it cannot send an ordinary packet.** It also does not know where the server is. Broadcasting to `255.255.255.255` solves both problems at once.

**A12 — B) one.** NAT rewrote every request's source to the router's single public address.

**A13 — C) outside port number.** The private addresses are invisible to the outside world, and the destination is the same for both. Only the port distinguishes them.

**A14 — B) a machine inside starts an outbound connection.** This single fact explains both why NAT provides incidental protection and why inbound connections are impossible without a tunnel.

**A15 — B) the address is private and there is no NAT row for an unsolicited packet.** Two problems at once: `192.168.0.11` cannot be addressed from the internet at all, and even a packet arriving at the public address would match no row, so the router could not know which laptop it was for.

**A16 — B) your machine makes an outbound connection first, and traffic returns down it.** Nothing on the router is reconfigured; the tunnel exploits the one case NAT permits.

**A17 — C) the TTL in seconds.** How long the answer may be cached. It counts down on repeated queries.

**A18 — C) `A`.** `AAAA` is the IPv6 equivalent; `NS` names the authoritative servers; `PTR` is the reverse lookup.

**A19 — B) resolver → root → TLD → authoritative.** Right to left across the name: `.` then `com` then `codekaryashala`. In practice most of this is skipped because the resolver has it cached.

**A20 — C) your default gateway.** Every packet leaving your machine for anywhere else goes through it, so it is always the first hop.

**A21 — B) that router did not reply to the probe, but packets still pass through.** Proof: later hops still appear. If the packets had genuinely stopped, everything after would also be `*`.

**A22 — B) sending packets with increasing TTL and reading the expiry errors.** TTL 1 makes the first router complain, TTL 2 the second, and so on. The error messages are the map.

**A23 — B) routing follows the cables and agreements between networks, not the map.** There is no cable from Vizag to San Jose. Traffic follows the undersea cables and the transit relationships that exist.

**A24 — B) once in total, at the edge router.** That is where the single public address lives. Traffic between classrooms is ordinary routing with no translation.

**A25 — B) it keeps broadcast traffic local and makes problems easier to isolate.** On one flat network of 600 machines, every DHCP Discover and every ARP would reach all 600.

**A26 — B) no, each device has its own session key.** WPA2/WPA3 derive a per-device key during the join handshake. The neighbour's card receives your radio signal and cannot decrypt it.

**A27 — C) who you connected to, when, and how much data moved.** Encryption hides the contents of the envelope, not the fact that it was sent, nor its size or timing.

**A28 — B) sends each frame only to the port of its destination.** A hub copied everything everywhere; a switch learns which device is on which port.

**A29 — B) serve content from a location near the user, cutting latency and origin load.**

**A30 — C) 2^128.** 16 bytes = 128 bits. Task 36's `2^(8N)` with N = 16.

**A31 — B) having enough addresses for every device to have a public one.** NAT exists only because addresses are scarce. Remove the scarcity and the reason disappears.

---

# Part B — Fill in the Blanks

**B1** — **4** bytes, each **0** to **255**.

**B2** — `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.

**B3** — **`169.254.`** (the link-local block `169.254.0.0/16`).

**B4** — the first **24** bits are the **network** part.

**B5** — **ANDs** it with the mask.

**B6** — the **default gateway**.

**B7** — **Discover**, **Offer**, **Request**, **Acknowledge**.

**B8** — a **lease**.

**B9** — **Network Address Translation**; the **port** number.

**B10** — **inside** the network.

**B11** — the **TTL**, in **seconds**.

**B12** — from **right** to **left**: root, then the **TLD** (`.com`), then the **authoritative** server.

**B13** — an increasing **TTL** (time to live / hop count).

**B14** — an **Autonomous System**.

**B15** — **one** hop; **HTTPS**.

**B16** — **anycast**.

---

# Part C — Scenario Questions

### C1 — three addresses, one identity

**(a)** All three share the first three bytes, `192.168.0`, and all have `/24`. That means they are on the **same subnet** — each can reach the others directly without involving the router, and each will compute the same network address, `192.168.0.0`, when it ANDs with the mask.

**(b)** All three see **the same** public address, say `49.207.41.133`. `ifconfig.me` reports the source address it saw on the arriving packet, and by the time the packet reached that server, NAT had rewritten the source to the router's single public address.

**(c)** There is no contradiction because the two answers describe **different sides of the router**. Inside, there really are three distinct machines with three distinct private addresses. Outside, there is one public address, and the router keeps a table mapping each active connection to a different **port** on that one address. The three identities are preserved — they just move from being encoded in the *address* to being encoded in the *port*.

### C2 — the machine with 169.254

**(a)** A **link-local** address from `169.254.0.0/16`. The machine assigned it to itself after no DHCP server answered its Discover. Nothing gave it out.

**(b)** Any two of: the WiFi is not actually associated (wrong password, out of range, adapter disabled); the DHCP server is down or not running on the router; the DHCP **pool is exhausted** so there was nothing left to lease; a cable or driver problem means the Discover never left the machine.

**(c)** First check whether the machine is genuinely connected to the network at all — `ip link` to confirm the interface is `UP`, and whether the WiFi shows as associated. That separates "no network connection" from "connected but no address", which need completely different fixes. Then `sudo dhclient -v eth0` and watch whether an Offer comes back.

### C3 — direct or gateway

Network is `192.168.0.11 & 255.255.255.0` = `192.168.0.0`.

**(a)** `192.168.0.1 & 255.255.255.0` = `192.168.0.0` — same → **direct**. (It happens to *be* the gateway, but it is reached as an ordinary neighbour.)
**(b)** `192.168.0.200` → `192.168.0.0` — same → **direct**.
**(c)** `192.168.1.11` → `192.168.1.0` — **different** → **gateway**. Note how close it looks to (b); only the third byte changed, and that byte is inside the network part.
**(d)** `8.8.8.8` → `8.8.8.0` — different → **gateway**.

### C4 — the pool runs out

**(a)** `192.168.0.100` to `192.168.0.200` inclusive is **101** addresses, so 101 devices at once.

**(b)** The first 101 get addresses. The remaining ~29 send Discover, get no Offer, and fall back to **`169.254.x.x`** — so their users see the WiFi as "connected" while nothing works, which is exactly the confusing symptom from C2.

**(c)** Two of: **enlarge the pool** within the same subnet (say `.20` to `.250`, giving 231) — simplest, no other change needed, and enough for a workshop; **shorten the lease time** so addresses from devices that have left are recycled quickly; **move to a larger subnet** such as `/23`, which allows 510 hosts but means reconfiguring the mask on the router. I would enlarge the pool: it is one setting, it needs no change on any client, and 231 comfortably covers 130.

### C5 — the NAT table

**(a)**

| Outside | Inside | Destination |
|---|---|---|
| `49.207.41.133:60001` | `192.168.0.11:51001` | `140.82.121.4:443` (github) |
| `49.207.41.133:60002` | `192.168.0.17:49500` | `103.102.166.224:443` (wikipedia) |
| `49.207.41.133:60003` | `192.168.0.23:51001` | `23.95.60.64:8000` (codekaryashala) |

**(b)** The router reads the destination port, **60002**, and looks it up in the table. It finds the row for Asha, rewrites the packet's destination address and port from `49.207.41.133:60002` to `192.168.0.17:49500`, and forwards it onto the LAN, where it is delivered to Asha's laptop.

**(c)** Because the inside port number is only meaningful **on the machine that chose it**. Ravi's `51001` and Balaji's `51001` live in two separate machines and never meet. What must be unique is the **outside** port, and the router guarantees that by assigning each connection a different one — 60001 and 60003 here. That is precisely the translation NAT performs.

### C6 — the link that works in the room but not outside

**(a)** Both machines are on `192.168.0.0/24`. The classmate's laptop ANDs `192.168.0.11` with its mask, sees its own network, and sends the packet **directly** — the router is barely involved and NAT never enters into it.

**(b)** From outside, two things fail. `192.168.0.11` is a **private** address that is not routed on the internet, so the packet cannot even be addressed. And if the brother instead tried the WiFi's public address, the arriving packet would match **no row in the NAT table** — rows exist only for connections started from inside — so the router has no way to know which of the machines in the room it is for, and drops it.

**(c)** Use a **tunnel** — `ngrok http 8000` — which gives a public URL. It succeeds because **the laptop makes an outbound connection to ngrok's public server first**. NAT permits outbound connections and creates a table row for that one, and everything afterwards travels back down that already-open connection. The brother never contacts the laptop; he contacts ngrok, which passes the request down the pipe. The direction of the *first* packet is the entire difference.

### C7 — the counting-down TTL

**(a)** It is **counting down**. The resolver cached the answer with a 1800-second lifetime; ten seconds later 1789 remain.

**(b)** In the **resolver's cache** — the DNS server your machine was told to use, here `8.8.8.8`. Your own machine and browser may also hold a shorter-lived copy.

**(c)** Students whose resolver still holds a cached copy get the **old** address until that copy expires; students whose resolver had nothing cached ask the authoritative server and get the **new** one immediately. The difference lasts at most as long as the **TTL** on the record. This is what "DNS propagation" means — nothing is propagating anywhere; caches are simply expiring at different moments. It is also why administrators lower the TTL to a minute or two *before* a planned change.

### C8 — silent hops

**(a)** They are hops that **did not reply** to the traceroute probe. The packets got through perfectly well — the proof is hop 13 answering. Many routers are configured not to respond to these probes, or to deprioritise them.

**(b)** A **very long link**, almost certainly an undersea cable to another continent. 148 ms of extra round trip is distance, not congestion: light in fibre covers roughly 200 km per millisecond, so ~150 ms round trip is on the order of 15,000 km of cable.

**(c)** **No.** If the network were broken between 7 and 12, nothing beyond it would answer and the destination would be unreachable. Those routers are simply staying quiet while forwarding normally. A `*` means "no reply to my probe", never "no path".

### C9 — where the server really is

**(a)** In the **United States** — the trace ends in San Jose, and the reverse lookup returns a `colocrossing.com` name, a hosting company. It is a rented machine, not a computer in Amalapuram.

**(b)** **Nothing at all.** A domain name is a label in a database that points at an address; it carries no geographic meaning. `.in` domains can be hosted anywhere, and `.com` domains are hosted everywhere. Only measurement — traceroute, latency, a reverse lookup — tells you where a server actually is.

**(c)** Because there is no direct cable from India to California. Traffic follows the **undersea cables and the commercial agreements between networks**: from ACT to a transit provider (Tata), across to a major European exchange, then across the Atlantic on a backbone network (Telia) and across North America. Each network only knows "for this address range, hand it to that neighbour" — the full path is never planned by anyone.

**(d)** Serve the content from a machine near the students — a **CDN**, or simply hosting in an Indian data centre. Either would cut the round trip from ~346 ms to tens of milliseconds.

### C10 — the college

**(a)** The sending machine ANDs and finds a **different** network, so it sends to its gateway, `192.168.4.1`. The **core switch** consults its routing table, sees `192.168.5.0/24`, and forwards out of the room-5 port. **No NAT** — both addresses are private and internal, and no translation is needed or wanted.

**(b)** Machine → its gateway → core switch, which finds no matching classroom route and so uses its **default route** to the edge router → **NAT happens here**, at the edge, where the single public address is → out to the internet.

**(c)** **Lookup one, at the edge router:** port 61001 is found in the NAT table, and the destination is rewritten to `192.168.4.12`. **Lookup two, at the core switch:** the destination `192.168.4.12` matches the `192.168.4.0/24` route, so the packet goes out of the room-4 port and reaches machine 12.

**(d)** Because a single flat network of 600 machines means **every broadcast reaches all 600** — every DHCP Discover, every ARP request. It also makes faults hard to isolate, prevents per-room policies, and means one misbehaving machine affects everybody. Subnets keep broadcast traffic local and give each room a boundary.

### C11 — "our WiFi has a password, so we are safe"

**(a)** Correct that the WiFi password means **their neighbours in the room cannot read their traffic** — WPA2 gives each device a separate session key, so the radio signal is useless to anyone else present.

**(b)** Wrong that this extends "all the way to the website". WiFi encryption protects exactly **one hop**: laptop to router. Beyond it, the packet crosses ACT, Tata, Telia and several countries — as the traceroute showed — and at every one of those hops it is on somebody else's equipment, with the WiFi encryption long since removed.

**(c)** On open WiFi with `http://`, everything is exposed: the **hostname**, the **URL path**, the **form data including the password**, and the **entire response**. Exposed to **anyone within radio range** running a capture, since there is no encryption at the WiFi layer either — and also to every network along the path.

**(d)** Using **`https://`**. It encrypts the path, the form data and the response end to end, so neither the person in the airport nor any intermediate network can read them.

### C12 — different answers to the same question

**(a)** `www.wikipedia.org` is served by a **CDN**, which deliberately answers with an address near whoever asked. Both answers are correct; they point at different copies.

**(b)** **DNS-based steering** — the CDN's DNS server notes roughly where the query came from and returns a nearby address. And **anycast** — many servers share one address, and the routing system naturally delivers the packet to the topologically nearest.

**(c)** Three of: **offload** — the origin serves one copy and the CDN serves it a million times; **availability** — if one edge fails another answers; **absorbing attacks** — a flood is spread across hundreds of edges rather than hitting one server; **cost** — bandwidth at the edge is cheaper than from the origin.

### C13 — adopting IPv6

**(a)** **2^128**, about 3.4 × 10^38, against IPv4's 2^32 ≈ 4.3 × 10^9. Roughly 10^29 IPv6 addresses for every single IPv4 address.

**(b)** Three of: **NAT becomes unnecessary**, because every device can hold a public address; **SLAAC** lets a device build its own address from the router's advertisement instead of needing a DHCP server; **no broadcast**, replaced by targeted multicast, which reduces noise; a **fixed 40-byte header** with no per-hop checksum, so routers forward faster; **fragmentation** is done only by the sender.

**(c)** Because the two protocols are **not compatible** — an IPv6-only machine cannot talk to an IPv4-only server. Every network must therefore run **both at once** (dual stack) throughout the transition, which is real cost and effort for whoever does it, while the benefit accrues mostly to everyone else. That is a hard thing to justify network by network, which is why it has taken decades.

**(d)** **End-to-end reachability** — the restoration of direct addressing. Under IPv4, both devices sit behind NAT and cannot address each other, so almost every app is forced to relay everything through a central server. With IPv6 two devices can connect directly, which makes peer-to-peer, direct device-to-device features and IoT straightforward instead of requiring a relay in the middle. For a mobile app whose whole point is devices talking to each other, that is the difference between needing server infrastructure and not.

---

# Part D — Read the Output

**D1 —** Address **`192.168.0.11`** · mask **`255.255.255.0`** (from `/24`) · network **`192.168.0.0`** · broadcast **`192.168.0.255`** (stated as `brd`) · default gateway **`192.168.0.1`**.

**D2 —**

| Destination | AND with mask | Result |
|---|---|---|
| `192.168.0.1` | `192.168.0.0` | **direct** |
| `192.168.0.255` | `192.168.0.0` | **direct** — but it is the broadcast address, so it reaches every machine on the network at once |
| `192.168.10.5` | `192.168.10.0` | **gateway** |
| `127.0.0.1` | — | **neither** (see below) |
| `23.95.60.64` | `23.95.60.0` | **gateway** |

`127.0.0.1` is worth care. Applying the AND test mechanically gives `127.0.0.0`, which differs from `192.168.0.0`, and would suggest "send to the gateway" — and that is **wrong**. The real rule a machine follows is not "my subnet or else the gateway" but **longest prefix match**: it picks the most specific matching route in its table. There is a separate route for `127.0.0.0/8` pointing at the `lo` interface, and being more specific than the default route it wins. The packet is looped back inside the machine and never reaches the network. The AND test in Iteration 3 is a simplification of the real rule — accurate for the two-line routing table your laptop has, but the general mechanism is longest prefix match.

**D3 —** Name **`codekaryashala.com`** · type **`A`** (IPv4 address) · TTL **1799** seconds · address **`23.95.60.64`** · answered by **`8.8.8.8`** on port 53, which is a resolver rather than the authoritative server.

**D4 —**
(a) the **authoritative name servers** for the domain
(b) a **reverse lookup** — which name that address belongs to
(c) the **IPv6** address
(d) the **mail servers** that accept email for the domain

**D5 —** Your own router: **hop 1, `192.168.0.1`** — your default gateway. Last ISP hop: **hop 4, `broadband.actcorp.in`**. First hop belonging to a different company: **hop 5, Tata Communications in Pune** (`vsnl.net.in`). Private addresses: **hop 2 (`10.192.0.1`)** and **hop 6 (`172.25.81.134`)** — from `10.0.0.0/8` and `172.16.0.0/12`, used inside those providers' own networks.

**D6 —** `prs` **Paris** · `ash` **Ashburn, Virginia** · `ewr` **Newark** · `palo` **Palo Alto** · `sjo` **San Jose**.

The sequence says the packet left India, crossed to **Europe**, then crossed the **Atlantic** to the US east coast, then crossed **North America** to the west coast — with the round trip growing from 174 ms to 346 ms. It confirms that the destination is in the USA, not in India, and that the route is dictated by where the cables and interconnections are rather than by the shortest distance on a map.

**D7 —** The missing inside address is **`192.168.0.17:49500`** — Asha's machine, from C5. A reply to `49.207.41.133:60002` is rewritten to **`192.168.0.17`, port `49500`**, and delivered there.

**D8 —**

| Address | Kind |
|---|---|
| `10.1.1.1` | private |
| `172.31.5.5` | private — `172.16`–`172.31` is the block |
| `172.32.5.5` | **public** — just outside it |
| `192.168.1.1` | private |
| `127.0.0.1` | loopback |
| `169.254.5.5` | link-local — means DHCP failed |
| `224.0.0.1` | multicast |
| `49.207.41.133` | public |

**D9 —** `/24` → **254** · `/23` → **510** · `/16` → **65,534** · `/8` → **16,777,214**. In each case it is `2^(32 − prefix) − 2`, the two subtracted being the network and broadcast addresses.

**D10 —**

| | over `http://` | over `https://` |
|---|---|---|
| destination address | visible | **visible** |
| hostname | visible | usually visible |
| URL path | **visible** | encrypted |
| submitted password | **visible** | encrypted |
| response body | **visible** | encrypted |

The two rows that stay visible under HTTPS are the point: encryption protects the **contents**, not the **fact** of the conversation. An observer still learns who you talked to, when, and how much data moved.

---

## The pattern to notice

**Almost every answer is "because addresses are scarce".** Private ranges, NAT, tunnels, CGNAT, IPv6 — five separate topics that are all consequences of 2^32 being too small (A1, A2, A12, A16, A31, C13). Task 36's arithmetic really was the foundation of this whole worksheet.

**One decision, made over and over: is this local, or does it go to my gateway?** Your laptop makes it (C3), the core switch makes it one level up (C10), and every router on the path to San Jose makes the same kind of decision. Nobody knows the whole route; each device knows only its next step. The honest version of the rule is **longest prefix match** (D2), of which "my subnet or the gateway" is the two-line special case.

**Direction decides everything in NAT.** A row exists only for connections started inside. That single asymmetry explains incidental firewalling, the failure in C6, and why the tunnel in C6(c) works by connecting outward first.

**Caching is why things are fast and why changes are slow.** The same TTL that makes the second DNS lookup instant is what makes an address change take hours to be seen by everyone (C7). A CDN is the same trade applied to content (C12).

**Geography is not topology.** 200 km on a map was 20,000 km of cable through Paris (C9). Names tell you nothing about location; only measurement does.

**And the security lesson is a one-liner:** WiFi encryption covers one hop out of twenty. Only HTTPS covers the whole path (A26, A27, C11) — which is why every site you use has it, and why the classroom `python3 -m http.server` must never hold anything that matters.
