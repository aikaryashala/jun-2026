# How a Packet Finds You — Addresses, NAT, DNS and Routing

**Goal.** Task 36 ended by counting IPv4 addresses: four bytes, 2^32, about 4.3 billion. Today you find out how those addresses are handed out, why your laptop's address is one that millions of other laptops also have, and — the real question — how a reply to something *you* asked for crosses the world and lands on **your** machine and not the one next to you.

**You need:** your Linux VM (WSL), a terminal, and the AI Karyashala WiFi. Install the two tools this worksheet uses:

```
sudo apt update
sudo apt install -y dnsutils traceroute iproute2
```

**Every iteration asks you to run a command on your own machine and write down your own numbers.** The outputs printed here are from a real machine on a real ACT Fiber connection, but yours will differ — that is the point. Keep a page in your notebook headed "my network".

**Built on:** Task 36 (a byte is 0–255, an IPv4 address is four bytes), Task 10 (the `&` mask), Tasks 5 and 7 (the shell).

**Reference:** [**Task 35 — From a Bit to the Internet**](../task35/bits-to-internet.html) is the reading companion to this worksheet. It walks the same road as one continuous story, from a single bit all the way to a packet crossing the world. Read it before or after; this sheet is the hands-on version.

> **The golden rule of today**
> Your laptop's address is something like `192.168.0.11`. That address is **not on the internet**. Millions of other machines are using the very same number right now, and no packet from the outside world can be sent to it. Everything in this worksheet is the machinery that makes it work anyway.

---

## Iteration 1 — What is my address?

**a. What we set up**

Nothing. Two commands.

```
ip addr
ip route
```

**b. Task**

Before running, guess: does your laptop have one address or several? Then run both and compare your numbers with the person sitting next to you.

**c. Observation (what you should find)**

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    inet 127.0.0.1/8 scope host lo
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
    inet 192.168.0.11/24 brd 192.168.0.255 scope global eth0
```

```
default via 192.168.0.1 dev eth0
192.168.0.0/24 dev eth0 proto kernel scope link src 192.168.0.11
```

**Several addresses, not one.** Every network *interface* has its own:

- **`lo`** is the **loopback** — `127.0.0.1`, the machine talking to itself. It never touches a cable or the air. When you run `python3 -m http.server` and open `http://127.0.0.1:8000`, the packets do not leave your laptop at all.
- **`eth0`** (or `wlan0`) is the real one — `192.168.0.11`, given to you by the WiFi router.

Now compare with your neighbour. **The first three numbers are the same and the last is different:**

```
you        192.168.0.11
neighbour  192.168.0.17
another    192.168.0.23
```

That shared prefix is not a coincidence — it is what "being on the same network" means, and Iteration 3 explains exactly how your machine works it out.

Three more things in that output, each explained later today:

| What you saw | Means | Iteration |
|---|---|---|
| `/24` after the address | the **subnet mask** — how much of the address is the network | 3 |
| `brd 192.168.0.255` | the **broadcast** address for this network | 3 |
| `default via 192.168.0.1` | the **default gateway** — where to send anything not local | 3 |

**Write in your notebook now:** your address, your `/number`, and your default gateway. You will use all three today.

**Takeaway to say out loud:** "My address is `192.168.0.something` — I share the first three numbers with everyone else on this WiFi."

---

## Iteration 2 — Four billion addresses, and who already owns them

**a. What we set up**

From Task 36: an IPv4 address is **four bytes**, each 0–255, so there are

```
256 × 256 × 256 × 256 = 256^4 = 2^32 = 4,294,967,296
```

about 4.3 billion. There are more than 8 billion people. The shortage is real, and it shapes everything in this worksheet.

Worse, a large slice of those 4.3 billion **cannot be used on the public internet at all**, because they are reserved.

**b. Task**

Look at your own address from Iteration 1. Find it in the table below. Is it a public address or a private one?

**c. Observation (what you should find)**

**The private ranges — RFC 1918.** These are the addresses anyone may use inside their own network. They are *deliberately* not routed on the internet: no packet from outside can be addressed to one.

| Range | Written as | How many | Where you see it |
|---|---|---|---|
| `10.0.0.0` – `10.255.255.255` | `10.0.0.0/8` | 16,777,216 | big companies, ISPs, your `10.192.0.1` hop today |
| `172.16.0.0` – `172.31.255.255` | `172.16.0.0/12` | 1,048,576 | Docker's default, many corporate networks |
| `192.168.0.0` – `192.168.255.255` | `192.168.0.0/16` | 65,536 | **home and office WiFi — this is us** |

Your `192.168.0.11` is in the third one. **So is the laptop of someone in Delhi, and someone in Brazil, right now.** That is allowed precisely because these addresses are never visible outside their own network.

**The other reserved blocks:**

| Range | Purpose |
|---|---|
| `0.0.0.0/8` | "this network" — `0.0.0.0` also means "any address" when a server listens |
| `127.0.0.0/8` | **loopback** — the whole block, though everyone uses `127.0.0.1` |
| `100.64.0.0/10` | **carrier-grade NAT** — your ISP's own private space, when even they have run out |
| `169.254.0.0/16` | **link-local** — what your machine gives itself when DHCP fails. Seeing this means "no network" |
| `224.0.0.0/4` | **multicast** — one packet delivered to many subscribers |
| `240.0.0.0/4` | reserved, never allocated |
| `255.255.255.255` | **broadcast** — everyone on this network |
| `192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24` | reserved **for documentation** — safe to use in examples and books |

**The historical classes.** Before 1993 the address space was cut into fixed classes, decided by the leading bits — and you will still hear the names:

| Class | Starts | Leading bits | Network part | Meant for |
|---|---|---|---|---|
| A | 0–127 | `0` | first byte | enormous networks (16.7M hosts each) |
| B | 128–191 | `10` | first two bytes | medium (65,534 each) |
| C | 192–223 | `110` | first three bytes | small (254 each) |
| D | 224–239 | `1110` | — | multicast |
| E | 240–255 | `1111` | — | reserved |

The classes wasted addresses badly — an organisation needing 300 machines had to take a Class B and waste 65,000. They were replaced by **CIDR**, where the split can fall anywhere, written as `/24`, `/16`, `/12`. That is the `/24` you saw in Iteration 1, and Iteration 3 is about what it means.

**Takeaway to say out loud:** "`192.168.x.x` is private — millions of machines share it, and no packet from outside can be sent to it."

---

## Iteration 3 — The subnet mask, and the gateway

Your machine has to answer one question before sending *any* packet: **is the destination on my own network, or somewhere else?** The subnet mask is how it decides, and the answer is Task 10's `&` operator.

**a. What we set up**

`/24` means **the first 24 bits are the network**, the remaining 8 are the host. Written as four bytes, 24 ones followed by 8 zeros:

```
11111111 11111111 11111111 00000000   =   255 . 255 . 255 . 0
```

To find the network, your machine ANDs the address with the mask — bit by bit, exactly as in Task 10.

**b. Task**

Work these out on paper, then check with `ipcalc` or by hand. Your machine is `192.168.0.11/24`, so your network is `192.168.0.0`.

For each destination: AND it with `255.255.255.0` and compare with `192.168.0.0`.

1. `192.168.0.17` (your neighbour)
2. `192.168.0.254`
3. `192.168.5.11`
4. `23.95.60.64` (codekaryashala.com)

**c. Observation (what you should find)**

```
192.168.0.11    & 255.255.255.0 = 192.168.0.0    same  -> send DIRECT
192.168.0.17    & 255.255.255.0 = 192.168.0.0    same  -> send DIRECT
192.168.0.254   & 255.255.255.0 = 192.168.0.0    same  -> send DIRECT
192.168.5.11    & 255.255.255.0 = 192.168.5.0    NOT   -> send to the GATEWAY
23.95.60.64     & 255.255.255.0 = 23.95.60.0     NOT   -> send to the GATEWAY
```

**That single AND is the whole decision.** If the masked destination matches your own masked address, the other machine is on this WiFi and your laptop sends the packet straight to it. If it does not match, your laptop does not know and does not care where the destination is — it hands the packet to the **default gateway** and lets it worry.

The gateway is `192.168.0.1` — the WiFi router. It is the door out of the room. `ip route` said exactly this:

```
default via 192.168.0.1 dev eth0        <- everything else goes here
192.168.0.0/24 dev eth0 ... src 192.168.0.11   <- this network is directly reachable
```

Read that as a two-line instruction manual: *"For `192.168.0.x`, shout down the wire. For anything else, give it to `192.168.0.1`."*

**What `/24` gives you:**

| | |
|---|---|
| network address | `192.168.0.0` — names the network, not usable by a machine |
| broadcast address | `192.168.0.255` — reaches everyone at once, not usable by a machine |
| usable addresses | `192.168.0.1` … `192.168.0.254` — **254 machines** |

254, not 256, because the first and last are spoken for. If AI Karyashala ever had 300 devices at once, `/24` would not be enough and the network would need `/23` (510 hosts) or `/16` (65,534).

| Mask | `/bits` | Usable hosts |
|---|---|---|
| `255.255.255.0` | `/24` | 254 |
| `255.255.254.0` | `/23` | 510 |
| `255.255.0.0` | `/16` | 65,534 |
| `255.0.0.0` | `/8` | 16,777,214 |

**Takeaway to say out loud:** "AND the destination with the mask. Same answer as mine — send direct. Different — send to the gateway."

---

## Iteration 4 — DHCP: where your address came from

You never typed `192.168.0.11` anywhere. Something gave it to you.

**a. What we set up**

The WiFi router runs a **DHCP server** — Dynamic Host Configuration Protocol. It owns a **pool** of addresses and hands them out on request.

**b. Task**

Watch it happen. Ask for a fresh address and see what arrives:

```
ip addr show eth0
sudo dhclient -v eth0
```

Then look at the lease your machine was given:

```
cat /var/lib/dhcp/dhclient.leases
```

**c. Observation (what you should find)**

DHCP is four messages, in a fixed order, remembered as **DORA**:

| Step | Who | Message | Meaning |
|---|---|---|---|
| **D** | your laptop | **Discover** | broadcast to `255.255.255.255`: *"is there a DHCP server?"* — it has no address yet, so it must shout to everyone |
| **O** | the router | **Offer** | *"you may have `192.168.0.11`"* |
| **R** | your laptop | **Request** | *"yes, I will take `192.168.0.11`"* — still broadcast, so any other DHCP server knows it was not chosen |
| **A** | the router | **Acknowledge** | *"it is yours for 24 hours"* |

Notice the first message is a **broadcast**. Your laptop cannot send a normal packet because it does not yet have an address to send it *from* — a chicken-and-egg problem solved by shouting to the whole network.

The Acknowledge carries more than the address. It is a complete starter kit:

```
your address     192.168.0.11
subnet mask      255.255.255.0
default gateway  192.168.0.1
DNS servers      192.168.0.1  (or 8.8.8.8)
lease time       86400 seconds (24 hours)
```

**Every number from Iterations 1 and 3 arrived in that one message.** You configured nothing.

**The pool.** The router is set up with a range, say `192.168.0.100` to `192.168.0.200` — 101 addresses. Devices get one each, in order, as they arrive:

| Device | Address | Lease expires |
|---|---|---|
| Ravi's laptop | 192.168.0.100 | tomorrow 09:15 |
| Asha's laptop | 192.168.0.101 | tomorrow 09:22 |
| Balaji's phone | 192.168.0.102 | tomorrow 09:30 |

**The lease is why your address changes.** It is a rental, not a purchase. Leave for the weekend, and when the lease expires the router may give `192.168.0.100` to somebody else. This is also why the pool can be smaller than the number of people who ever visit — only *simultaneous* devices need addresses.

And it is the first reason a service on your laptop is hard to reach from outside: **your address is not stable.** Even if someone could reach it, tomorrow it is a different number.

Two related ideas you will meet:

- **A static lease (DHCP reservation)** — the router is told "this particular device always gets `192.168.0.50`". Common for printers and servers.
- **Address exhaustion** — if 101 devices are already leased and a 102nd arrives, there is nothing to give. It gets `169.254.x.x` from Iteration 2 and nothing works.

**Takeaway to say out loud:** "DHCP is Discover, Offer, Request, Acknowledge — and my address is a lease that expires."

---

## Iteration 5 — NAT: how 30 laptops share one address

Here is the puzzle this worksheet exists to solve.

**a. What we set up**

Everyone in the room has a **private** address that no packet from outside can reach. Yet everyone browses the web perfectly well. And when the whole batch loads `http://codekaryashala.com:8000`, the server's log shows **one IP address for all of you**.

Check it. Find the WiFi's single public address:

```
curl -s ifconfig.me ; echo
```

**b. Task**

Everyone in the room runs that command. Compare the answers. Then explain how the reply to *your* request found *your* laptop, when the server only ever saw one address.

**c. Observation (what you should find)**

**Everybody gets the same answer** — one public address for the whole room, say `49.207.41.133`. The server genuinely cannot tell you apart by address.

The router performs **NAT — Network Address Translation**. On the way out it *rewrites* the packet, and it writes down what it did.

Three students, three different sites, at the same moment:

**Leaving the room** — the router replaces each private source with its own public address, and gives each connection a **different source port**:

| | Original packet (inside) | Rewritten packet (outside) |
|---|---|---|
| Ravi | `192.168.0.11:51001` → `140.82.121.4:443` (github.com) | `49.207.41.133:60001` → `140.82.121.4:443` |
| Asha | `192.168.0.17:49500` → `103.102.166.224:443` (wikipedia) | `49.207.41.133:60002` → `103.102.166.224:443` |
| Balaji | `192.168.0.23:51001` → `23.95.60.64:8000` (codekaryashala) | `49.207.41.133:60003` → `23.95.60.64:8000` |

Notice Ravi and Balaji both happened to use source port `51001` — that is fine, because they are on different machines. The router gives them **different** outside ports, and that is what keeps them apart.

**The NAT table** — the router's notebook:

| Outside port | Inside address | Inside port | Destination |
|---|---|---|---|
| 60001 | 192.168.0.11 | 51001 | 140.82.121.4:443 |
| 60002 | 192.168.0.17 | 49500 | 103.102.166.224:443 |
| 60003 | 192.168.0.23 | 51001 | 23.95.60.64:8000 |

**Coming back** — a reply arrives addressed to `49.207.41.133:60003`. The router looks up port 60003, finds the row, rewrites the destination back to `192.168.0.23:51001`, and sends it into the room.

```
reply arrives   ->  49.207.41.133:60003
look up 60003   ->  192.168.0.23:51001
rewrite and forward into the LAN
```

**The port number is the whole answer.** One public address, 65,535 possible ports, so one address can support tens of thousands of simultaneous connections. Because this shares one address across many machines *by port*, it is often called **PAT** (Port Address Translation) or **NAT overload** — and it is what nearly every home and office router does.

Now the consequence that matters, and it is worth saying plainly:

**A row appears in that table only when someone inside starts a connection.** A packet arriving from the internet with nothing matching in the table has no row to look up — the router cannot know which of the thirty laptops it belongs to, so it **drops it**.

This is why your `python3 -m http.server` cannot be reached from a friend's mobile data. Not a firewall, not a setting you forgot: **there is no row in the table, and there is no way for the router to guess.** NAT is a side-effect security feature and an obstacle at the same time.

**Takeaway to say out loud:** "NAT swaps my private address for the router's public one and remembers the port — replies come back by port number."

---

## Iteration 6 — Tunnelling: reaching in from outside

**a. What we set up**

You are building a mobile app. The app runs on a phone using mobile data; the API runs on your laptop on the AI Karyashala WiFi. From Iteration 5 you now know exactly why the phone cannot reach it.

You cannot ask ACT Fiber for a public address for your laptop, and you should not have to.

**b. Task**

Start a service, then expose it with a tunnel:

```
python3 -m http.server 8000
```

In another terminal:

```
ngrok http 8000
```

**c. Observation (what you should find)**

```
Forwarding    https://a1b2-49-207-41-133.ngrok-free.app -> http://localhost:8000
```

That URL works from **anywhere** — the phone on mobile data, a friend in another city. Nothing about the WiFi changed. No port was opened on the router.

**How it works, and why it gets past NAT.** The trick is the direction of the connection:

```
   your laptop                    ngrok server              the phone
   192.168.0.11                  (public address)          (mobile data)
        |                              |                        |
        |  1. OUTBOUND connection ---> |                        |
        |     (NAT allows this, and    |                        |
        |      writes a table row)     |                        |
        |                              | <--- 2. request to the |
        |                              |      public URL        |
        | <--- 3. sent down the        |                        |
        |     connection already open  |                        |
        |                              |                        |
        |  4. reply ------------------>| ---------------------> |
```

**Step 1 is the whole trick.** Your laptop connects *out* to ngrok's public server. NAT allows outbound connections and creates a table row for it — that is the case it is built for. That connection then stays open, and everything afterwards travels along it in both directions. The phone never contacts your laptop; it contacts ngrok, which passes the request down a pipe your laptop already opened.

**A tunnel turns "unreachable from outside" into "reachable", using only an outbound connection.**

Where you will meet this idea again: `ssh -R` remote port forwarding, Cloudflare Tunnel, VS Code / JetBrains remote development, and every "webhook testing" tool. They are all the same manoeuvre.

Two cautions worth stating. That URL is **public** — anyone with it can reach your machine, so do not tunnel anything you have not thought about. And on the free tier the URL changes each time you restart.

**Takeaway to say out loud:** "A tunnel works because *I* connect outward first — NAT allows that, and everything else rides back down the same connection."

---

## Iteration 7 — DNS: from a name to an address

You typed `codekaryashala.com`, not `23.95.60.64`. Something translated it.

**a. What we set up**

```
dig codekaryashala.com
```

**b. Task**

Predict what a name lookup returns besides the address. Then run it, and run it a second time watching the number in the middle column.

**c. Observation (what you should find)**

```
;; QUESTION SECTION:
;codekaryashala.com.		IN	A

;; ANSWER SECTION:
codekaryashala.com.	1799	IN	A	23.95.60.64

;; Query time: 73 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
```

Read the answer line: the **name**, the **TTL** (1799 seconds — how long this answer may be cached), the class `IN` (internet), the **type** `A` (an IPv4 address), and the **address**.

Run it again and the TTL will have *dropped* — 1799, then 1780, then 1500. It is counting down. When it hits zero the cached copy is thrown away and the next lookup goes and asks again. This is why a DNS change "takes time to propagate": everyone's cached copy has to expire first.

**Just the answer:**

```
$ dig +short codekaryashala.com
23.95.60.64
```

**Other record types:**

```
$ dig +short NS codekaryashala.com
dns1.registrar-servers.com.
dns2.registrar-servers.com.
```

| Type | Holds |
|---|---|
| `A` | an IPv4 address |
| `AAAA` | an IPv6 address |
| `NS` | which servers are authoritative for this name |
| `MX` | where to deliver email for this domain |
| `CNAME` | an alias — "this name is really that name" |
| `TXT` | free text, used for domain-ownership proofs |
| `PTR` | reverse: address → name |

**And the reverse lookup**, which tells you something interesting:

```
$ dig +short -x 23.95.60.64
23-95-60-64-host.colocrossing.com.
```

The server's real owner is a hosting company. `codekaryashala.com` is a name pointing at a rented machine.

### The resolution chain

Nobody holds a list of every domain. The answer is found by asking down a hierarchy, right to left:

```
        codekaryashala.com.
                        ^  ^   ^
                        |  |   +-- the root  "."
                        |  +------ the TLD   "com"
                        +--------- the domain "codekaryashala"
```

1. **Your machine** checks its own cache, and `/etc/hosts`.
2. It asks its **resolver** — the address DHCP gave it (your router, or `8.8.8.8`). This one does the real work, which is why it is called a *recursive* resolver.
3. The resolver asks a **root server**: *"who handles `.com`?"* The root does not know the address — it replies with the `.com` servers.
4. It asks a **`.com` server**: *"who handles `codekaryashala.com`?"* Answer: `dns1.registrar-servers.com`.
5. It asks **that** server, which is **authoritative** — it holds the real record and answers `23.95.60.64`.
6. The resolver caches the answer for the TTL and hands it to you.

Watch it yourself, one step at a time:

```
dig +trace codekaryashala.com
```

*(This asks each level directly instead of letting the resolver do it. Some networks block it; if you get no output, the chain above is still what happened.)*

Notice that steps 3–5 usually do not happen at all. The resolver is answering thousands of people and has almost everything cached — which is why the query above took 73 ms and the next one will take about 1 ms.

**Takeaway to say out loud:** "DNS walks right to left — root, then `.com`, then the domain's own server — and caches the answer for the TTL."

---

## Iteration 8 — `traceroute`: seeing every hop

You have an address. How does a packet actually get there?

**a. What we set up**

```
traceroute codekaryashala.com
```

**b. Task**

Guess how many machines a packet passes through on the way. Three? Ten? Thirty? Write your guess down, then run it.

**c. Observation (what you should find)**

Here is a real run from a machine on ACT Fiber:

```
traceroute to codekaryashala.com (23.95.60.64), 30 hops max, 40 byte packets
 1  192.168.0.1 (192.168.0.1)  3.351 ms
 2  10.192.0.1 (10.192.0.1)  5.359 ms
 3  106.51.40.153 (106.51.40.153)  5.362 ms
 4  broadband.actcorp.in (49.207.41.133)  5.979 ms
 5  115.113.104.117.static-pune.vsnl.net.in (115.113.104.117)  5.816 ms
 6  172.25.81.134 (172.25.81.134)  28.338 ms
 7  *
 8  *
```

Read it line by line, because every hop is something you already know:

| Hop | Address | What it is |
|---|---|---|
| 1 | `192.168.0.1` | **your default gateway** — the WiFi router, from Iteration 1 |
| 2 | `10.192.0.1` | a **private** address (Iteration 2) — inside ACT's own network |
| 3 | `106.51.40.153` | ACT, now on public addresses |
| 4 | `broadband.actcorp.in` | ACT Fiber's edge — **this is the last hop that belongs to your ISP** |
| 5 | `...static-pune.vsnl.net.in` | **Tata Communications, in Pune** — a different company entirely |
| 6 | `172.25.81.134` | private again, inside Tata's network |
| 7–8 | `*` | no reply — see below |

**How it works.** Every packet carries a **TTL** (time to live) — a hop counter. Each router decrements it, and any router that decrements it to zero throws the packet away and sends back an error. `traceroute` exploits this: it sends a packet with TTL 1 (the first router complains, revealing itself), then TTL 2, then 3, and so on. **The error messages are the map.**

**The `*` lines are not failures.** Many routers are configured not to reply to these probes. The packet still passes through perfectly well — that hop just declines to identify itself.

**The times tell a story.** Hop 1 is 3 ms — the same room. Hops 3–5 are about 5 ms — still nearby. Hop 6 jumps to 28 ms. Each jump is distance and equipment; a big jump usually means a long cable, often undersea.

Useful options:

| Option | Does |
|---|---|
| `-m 15` | stop after 15 hops |
| `-q 1` | one probe per hop instead of three (faster) |
| `-w 1` | wait only 1 second before giving up on a hop |
| `-n` | show numbers only, skip the name lookups (much faster) |

**Run it yourself now**, on the AI Karyashala WiFi, and write down your first five hops. Then run it to a completely different site and compare — **the first three or four hops will be identical**, because every packet you send leaves by the same door.

**Takeaway to say out loud:** "`traceroute` counts hops by letting the TTL expire — hop 1 is always my own gateway."

---

## Iteration 9 — Vizag to Amalapuram, the long way round

**a. What we set up**

Amalapuram is about 200 km from Vizag. If a server were there, you might expect a short trip. Let us follow the packet all the way and see.

**b. Task**

Predict: for a destination a few hundred kilometres away, how many hops, and roughly what round-trip time?

```
traceroute -n -q 1 -m 22 codekaryashala.com
```

**c. Observation (what you should find)**

Continuing the same real run:

```
 4  broadband.actcorp.in (49.207.41.133)      5.979 ms     <- ACT, Vizag
 5  115.113.104.117.static-pune.vsnl.net.in   5.816 ms     <- Tata, Pune
 6  172.25.81.134                            28.338 ms
 7-12  *
13  80.231.245.15                           154.617 ms
14  prs-bb2-link.ip.twelve99.net            173.762 ms     <- PARIS
15  ash-bb2-link.ip.twelve99.net            278.682 ms     <- ASHBURN, USA
16  ewr-bb2-link.ip.twelve99.net            271.275 ms     <- NEWARK
19  rest-bb1-link.ip.twelve99.net           268.438 ms     <- RESTON
20  palo-bb4-link.ip.twelve99.net           269.023 ms     <- PALO ALTO
21  den-bb1-link.ip.twelve99.net            289.323 ms     <- DENVER
22  sjo-bb3-link.ip.twelve99.net            345.965 ms     <- SAN JOSE
```

**Read the router names.** Network operators encode the city in the hostname, usually as an airport code:

| In the name | City |
|---|---|
| `prs` | Paris |
| `ash` | Ashburn, Virginia |
| `ewr` | Newark |
| `palo` | Palo Alto |
| `den` | Denver |
| `sjo` | San Jose |

`twelve99.net` is Telia/Arelion, one of the handful of companies whose networks form the backbone of the internet.

**Two findings, and both matter.**

**First: the server is not in Amalapuram at all.** Iteration 7's reverse lookup said `colocrossing.com` — a hosting company. `codekaryashala.com` is a rented machine in the United States. A domain name tells you *nothing* about where the computer is.

**Second, and this is the real lesson: geographic distance is not network distance.** The packet went Vizag → Pune → Paris → Ashburn → Newark → Reston → Palo Alto → Denver → San Jose. It crossed the Indian Ocean, went to Europe, crossed the Atlantic, and then crossed North America twice. The round trip grew from 6 ms to 346 ms.

**Why would a packet to America go via Paris?** Because there is no cable from Vizag to San Jose. There are **undersea cables** landing at particular places, and traffic follows them:

1. Your packet reaches ACT's network and then a **transit provider** (Tata) — a company that carries traffic between networks.
2. Tata takes it to a cable landing station and across the sea to a large exchange point — Europe is the usual route from India.
3. From there another backbone (Telia) carries it across the Atlantic and across the USA.
4. Finally it reaches the hosting company's network, and the server.

Each of these companies is an **AS — Autonomous System** — a network under one organisation's control, with a number. They agree who carries whose traffic, using **BGP**, the protocol by which networks tell each other "you can reach these addresses through me". **No single router knows the whole path.** Each one knows only "for this range of addresses, the next step is that neighbour". The path in your traceroute was never planned by anybody — it emerged, hop by hop.

**So what would a genuinely nearby server look like?** Try one and compare:

```
traceroute -n -q 1 www.iitm.ac.in
```

You should see far fewer hops and times in the tens of milliseconds — traffic staying inside India, likely handed over at an exchange in Chennai, Mumbai or Hyderabad.

**And this is exactly why CDNs exist** — which is Iteration 12.

**Takeaway to say out loud:** "Geography is not topology. My packet went to Paris to reach America, and the domain name told me nothing about where the server is."

---

## Iteration 10 — One broadband line, many classrooms

**a. What we set up**

AI Karyashala is one room with one router. A college has thirty classrooms, several hundred machines, and still just one broadband connection. How does a reply find room 4, machine 12?

**b. Task**

Before reading: you already know the answer's two halves. Which idea gets a packet to the right *classroom*, and which gets it to the right *machine*?

**c. Observation (what you should find)**

It is the same two mechanisms, applied twice — and nothing new is needed.

```
                                    ONE public address  203.0.113.7
                                             |
                                    +--------+--------+
                                    |   edge router   |   <- NAT happens HERE, once
                                    |  (does the NAT) |
                                    +--------+--------+
                                             |
                                    +--------+--------+
                                    |  core / layer-3 |   <- routes BETWEEN classrooms
                                    |     switch      |
                                    +--+-----+-----+--+
                                       |     |     |
                    192.168.4.0/24 ----+     |     +---- 192.168.6.0/24
                          room 4             |               room 6
                                       192.168.5.0/24
                                            room 5
```

**Each classroom is its own subnet.** Room 4 is `192.168.4.0/24`, room 5 is `192.168.5.0/24`, and so on — 254 machines each, and the third byte is the room number. A machine in room 4 has gateway `192.168.4.1`; in room 5 it is `192.168.5.1`.

**Now apply Iteration 3.** A machine in room 4 sending to `192.168.4.30` ANDs with the mask, sees the same network, and sends direct. Sending to `192.168.5.30` it gets a *different* network and hands the packet to its gateway. The **core switch** holds a routing table:

| Destination | Send to |
|---|---|
| `192.168.4.0/24` | port for room 4 |
| `192.168.5.0/24` | port for room 5 |
| `192.168.6.0/24` | port for room 6 |
| `0.0.0.0/0` (anything else) | the edge router |

That last row is the **default route** — the same idea as your laptop's default gateway, one level up. Each device only ever knows "same network → direct; otherwise → my gateway", and gateways chain.

**And NAT happens once, at the edge.** The edge router keeps one table for the entire college, and its rows now record which *classroom subnet* as well as which machine:

| Outside port | Inside address | Where that is |
|---|---|---|
| 61001 | 192.168.4.12 | room 4, machine 12 |
| 61002 | 192.168.5.30 | room 5, machine 30 |
| 61003 | 192.168.4.12 | room 4, machine 12 — a second tab |

A reply to `203.0.113.7:61001` is looked up, rewritten to `192.168.4.12`, and given to the core switch, which sees `192.168.4.x`, and sends it out of the room-4 port. **Two lookups, and it lands on the right desk.**

Note the third row: the same machine has two connections open, distinguished only by outside port. That is why the port matters more than the address.

**Why subnet per classroom instead of one huge `/16`?** Because a single flat network of 600 machines means every broadcast — every DHCP Discover, every ARP — reaches all 600. Splitting into subnets keeps broadcasts local, makes problems easier to isolate, and lets rules be applied per room. Often each subnet is also a **VLAN**, which keeps rooms separated even when they share physical switches.

**Takeaway to say out loud:** "Each classroom is a subnet, the core switch routes between them, and NAT happens once at the edge."

---

## Iteration 11 — What can be seen on the network

WiFi is radio. Your packets are broadcast into the air of the room, and every device present receives the signal. So can your neighbour read what you are doing?

**a. What we set up**

This iteration is about understanding what is and is not visible on a network you are responsible for, and why the defences exist. **Capture only your own machine's traffic.**

**b. Task**

Watch your own traffic while you make a request:

```
sudo tcpdump -i any -n host 23.95.60.64
```

In another terminal, `curl http://codekaryashala.com:8000`. Then try the same against an HTTPS site and compare what is readable.

**c. Observation (what you should find)**

You will see the packets — addresses, ports, sizes, timing — and for the plain-HTTP request you can see the **contents** too, including the path requested.

Now the important part: **what about everybody else's traffic?**

**On WiFi.** The radio does reach every device, but WPA2 and WPA3 encrypt each device's traffic with a **different key**. When you join, a four-way handshake derives a session key unique to your device. Your neighbour's card receives your radio signal and gets nothing but noise from it.

| Network | Can a neighbour read your traffic? |
|---|---|
| **WPA2/WPA3 with a password** (AI Karyashala) | **No** — per-device session keys |
| **WPA3 specifically** | No, and it also prevents the offline password-guessing attack WPA2 allows |
| **Open WiFi, no password** (cafés, airports) | **Yes** — nothing is encrypted at the WiFi layer |

That last row is the one to remember. On open WiFi, everything not protected at a higher layer is readable by anyone in range.

**On a wired LAN.** An old **hub** copied every frame to every port, so anyone could see everything. A modern **switch** learns which device is on which port and sends each frame **only** to its destination. So a machine plugged into a switch sees its own traffic and broadcasts, and nothing else. Seeing more requires deliberate action — configuring a **mirror port** (what network administrators use to monitor legitimately), or an attack such as ARP spoofing, which tricks machines into sending frames via the attacker.

**The real lesson: none of this is what actually protects you.** WiFi encryption protects one hop — laptop to router. Your packet then crosses ACT, Tata, Telia and several countries, as Iteration 9 showed, and at every one of those hops it is somebody else's cable.

**What protects the contents end to end is HTTPS**, and this is why:

| | `http://` | `https://` |
|---|---|---|
| Address and port | visible to every hop | visible to every hop |
| Hostname | visible | usually visible |
| Path (`/login`) | **visible** | encrypted |
| Form data, passwords | **visible** | encrypted |
| Response body | **visible** | encrypted |

Notice what stays visible even with HTTPS: **who you talked to, when, and how much data**. Encryption hides the contents of the envelope, not the fact that it was sent.

Two practical rules that follow: never send anything you care about over plain `http://`, especially on open WiFi; and the `python3 -m http.server` you have been using is plain HTTP with no authentication — fine for a classroom, never for anything real.

*(One legal and ethical note, and it is not a formality: capture traffic only on networks you own or have written permission to test, and only your own traffic here. In many countries — India included — intercepting other people's communications without authorisation is a criminal offence. The purpose of this iteration is to understand why HTTPS matters and what a network administrator can legitimately see.)*

**Takeaway to say out loud:** "WiFi encryption protects one hop. Only HTTPS protects the contents all the way to the server."

---

## Iteration 12 — CDNs: moving the server closer

Iteration 9 showed a 346 ms round trip to a server in California. Every image on a page paying that cost would make the web unusable. CDNs are the fix.

**a. What we set up**

A **CDN — Content Delivery Network** keeps copies of a site's files on servers in many cities, and sends you to the nearest one.

**b. Task**

Ask for the same name twice and look at what comes back:

```
dig +short www.wikipedia.org
```

**c. Observation (what you should find)**

```
dyna.wikimedia.org.
103.102.166.224
```

Two lines: a `CNAME` — *"this name is really that name"* — and then an address. That `dyna.` prefix is the giveaway: the answer is produced dynamically, and **someone in another country asking the identical question gets a different address.**

How you are sent to a nearby copy:

- **DNS-based** — the CDN's DNS server notices roughly where the question came from and answers with a nearby address. Same name, different answer per region.
- **Anycast** — many servers around the world are given **the same IP address**, and the routing system naturally delivers your packet to the topologically nearest one. `8.8.8.8` works this way; you are not talking to a machine in America.

What a CDN is for:

| Purpose | What it means |
|---|---|
| **Latency** | a 20 ms hop to Chennai instead of 346 ms to San Jose |
| **Offload** | the origin server serves one copy; the CDN serves it a million times |
| **Availability** | one edge fails, another answers |
| **Absorbing attacks** | a flood hits hundreds of edges, not your one server |
| **Cost** | bandwidth at the edge is cheaper than from the origin |

**What gets cached.** Static things — images, CSS, JavaScript, video — sit at the edge for hours or days. Personalised content usually cannot be cached, so the request still travels to the origin; even then the CDN helps, because the connection is established quickly to a nearby edge.

`codekaryashala.com` has no CDN — one server, one address, and every student's request travels to California and back. For a classroom that is fine. For a site with real users it would not be.

**Takeaway to say out loud:** "A CDN answers the same name with a nearer address — the fix for the 346 ms in Iteration 9."

---

## Iteration 13 — IPv6: what happens when you stop counting

**a. What we set up**

Everything so far has been a workaround for one fact: 4.3 billion addresses are not enough. IPv6 removes the constraint by making the address **16 bytes instead of 4**.

**b. Task**

From Task 36: N bytes hold `2^(8N)` patterns. Work out how many addresses 16 bytes gives, before reading on. Then check whether your machine has one:

```
ip -6 addr
dig AAAA www.google.com +short
```

**c. Observation (what you should find)**

```
4 bytes  = 32 bits  = 2^32  = 4,294,967,296                                    (4.3 × 10^9)
16 bytes = 128 bits = 2^128 = 340,282,366,920,938,463,463,374,607,431,768,211,456   (3.4 × 10^38)
```

The comparison that makes it real: **IPv6 has about 10^29 addresses for every single IPv4 address.** There are enough for every grain of sand on Earth to have its own network.

They are written as eight groups of four hex digits, with `::` standing in for one run of zeros:

```
2001:0db8:0000:0000:0000:ff00:0042:8329
2001:db8::ff00:42:8329          (the same address, shortened)
::1                             (loopback — the IPv6 127.0.0.1)
```

**What the abundance actually enables:**

| | IPv4 today | IPv6 |
|---|---|---|
| **NAT** | required — one public address shared by everyone | **not needed** — every device can have its own public address |
| **Reachable from outside** | no, without a tunnel (Iteration 6) | yes, if the firewall permits |
| **Getting an address** | DHCP server hands out a lease | **SLAAC** — the device builds its own from the router's advert |
| **Broadcast** | yes, and it disturbs everyone | **none** — replaced by targeted multicast |
| **Header** | variable, with a checksum every router recomputes | fixed 40 bytes, simpler and faster to forward |
| **Fragmentation** | routers may fragment | sender only, so routers do less work |

The change that matters most for you as a developer is the second row. **End-to-end reachability is restored.** Under IPv4, everything ordinary people run sits behind NAT and cannot be addressed, which is why so much of the internet had to become "everybody connects out to a big central server". With IPv6 two devices can address each other directly — which makes peer-to-peer, direct device-to-device links, and IoT genuinely straightforward instead of requiring a relay service in the middle.

**Why is IPv4 still here, then?** Because the two are not compatible — an IPv6-only machine cannot talk to an IPv4-only server. Every network must therefore run both (**dual stack**) during the transition, which is extra work with no immediate benefit to whoever does it. Adoption is well past 40% of Google's traffic worldwide and still climbing. Mobile networks in India were among the fastest adopters; your phone very likely has an IPv6 address right now, even if your laptop does not.

If `ip -6 addr` showed only `::1`, your WiFi is IPv4-only — which is still very common for wired broadband.

**Takeaway to say out loud:** "IPv6 is 16 bytes instead of 4 — enough addresses that NAT stops being necessary at all."

---

## Practice — Predict the answer

Write every answer down **before** checking.

**P1.** Your machine shows `inet 192.168.0.11/24`. What is the subnet mask in dotted form, and how many usable addresses does the network have?

**P2.** Is `192.168.0.11` a public or private address? Can a server in America send a packet to it directly?

**P3.** Which of these are private: `10.5.5.5`, `172.20.1.1`, `172.40.1.1`, `192.168.9.9`, `8.8.8.8`?

**P4.** Your machine has address `192.168.0.11/24`. For each destination, say direct or via the gateway: (a) `192.168.0.99` (b) `192.168.3.99` (c) `127.0.0.1` (d) `23.95.60.64`

**P5.** What are the four DHCP messages, in order? Why must the first be a broadcast?

**P6.** You see `169.254.11.23` on your interface. What has gone wrong?

**P7.** Thirty students load `codekaryashala.com:8000`. How many different IP addresses does the server's log show, and why?

**P8.** In a NAT table, what single field lets the router tell two students' connections apart when both went to the same website?

**P9.** Why can a friend on mobile data not reach `http://192.168.0.11:8000`? Give the reason in terms of the NAT table.

**P10.** Explain in one sentence why ngrok works when opening a port on the router does not.

**P11.** In `codekaryashala.com. 1799 IN A 23.95.60.64`, what is the `1799` and what happens when it reaches zero?

**P12.** Put these in the order DNS asks them: the `.com` servers, the domain's authoritative server, your resolver, the root servers.

**P13.** What does hop 1 of a traceroute always show on your machine?

**P14.** A traceroute line shows `*`. Did the packet fail to get through?

**P15.** Round-trip times jump from 6 ms at hop 5 to 154 ms at hop 13. What most likely happened in between?

**P16.** A server is 200 km away but the packet visits Paris. Explain why, in one sentence.

**P17.** A college has rooms on `192.168.4.0/24` and `192.168.5.0/24`. A machine in room 4 sends to `192.168.5.30`. What does it do with the packet, and why?

**P18.** How many times does NAT happen in that college — once per classroom, or once in total? Where?

**P19.** On AI Karyashala's password-protected WiFi, can your neighbour read your traffic? What if the WiFi had no password?

**P20.** With HTTPS, what can an intermediate network still see about your connection?

**P21.** Why does `dig +short www.wikipedia.org` give a different address to someone in London?

**P22.** How many addresses does IPv6 have, as a power of 2? Name the mechanism it makes unnecessary.

---

### Self-check

**Cover this until every answer is written down.**

**P1** — `255.255.255.0`. 254 usable — `192.168.0.1` to `192.168.0.254`; the network address `.0` and the broadcast `.255` cannot be given to a machine.

**P2** — **Private** (`192.168.0.0/16`). **No.** Private addresses are not routed on the internet; a packet addressed to it would be dropped long before reaching us. Replies only arrive because NAT rewrote the address on the way out.

**P3** — Private: `10.5.5.5`, `172.20.1.1`, `192.168.9.9`. Public: `172.40.1.1` (the private block is only `172.16`–`172.31`) and `8.8.8.8` (Google's public resolver).

**P4** — (a) **direct** — same network. (b) **gateway** — `192.168.3.0` ≠ `192.168.0.0`. (c) **neither** — loopback never leaves the machine. (d) **gateway**.

**P5** — **Discover, Offer, Request, Acknowledge.** The first must be a broadcast because the machine has no address yet, so it cannot send an ordinary packet or know where the server is.

**P6** — **DHCP failed.** `169.254.x.x` is link-local, which a machine assigns itself when no DHCP server answered. Either the WiFi is not really connected or the pool is exhausted.

**P7** — **One.** NAT rewrote every request's source to the router's single public address, so every entry in the log shows the same one.

**P8** — The **outside port number**. Each connection is given a different one, and it is what the reply is looked up by.

**P9** — Because a NAT row is only created when someone **inside** starts a connection. An unsolicited packet from outside matches no row, so the router has no way to know which of the thirty laptops it is for, and drops it. (`192.168.0.11` is also private, so it could not be addressed from outside in the first place.)

**P10** — Because **your laptop makes the outbound connection**, which NAT allows and records, and all later traffic travels back down that already-open connection.

**P11** — The **TTL**, in seconds — how long the answer may be cached. It counts down; at zero the cached copy is discarded and the next lookup asks again.

**P12** — Your **resolver** → the **root** servers → the **`.com`** servers → the domain's **authoritative** server.

**P13** — Your **default gateway** — the WiFi router, `192.168.0.1`. Every packet leaving your machine goes through it.

**P14** — **No.** The packet passed through fine; that router simply chose not to reply to the probe. Later hops still appearing proves it.

**P15** — A very long link, almost certainly an **undersea cable** to another continent. Distance costs time no equipment can avoid.

**P16** — Because routing follows the **cables and the commercial agreements between networks**, not the map — there is no direct cable from here to there, and the path goes via the exchange points that do exist.

**P17** — It ANDs `192.168.5.30` with its mask, gets `192.168.5.0`, sees that this differs from its own `192.168.4.0`, and therefore sends the packet to **its gateway** (`192.168.4.1`) for the core switch to route.

**P18** — **Once in total**, at the **edge router**, because that is where the single public address is. Traffic between classrooms is ordinary routing and is not translated at all.

**P19** — **No** — WPA2/WPA3 gives each device its own session key, so a neighbour receives the radio signal but cannot decrypt it. On an **open** network with no password, **yes** — anything not protected by HTTPS is readable by anyone in range.

**P20** — **Who** you connected to (address, usually the hostname), **when**, and **how much** data moved. The path, the form data and the response body are encrypted. Encryption hides the contents, not the fact of the conversation.

**P21** — Because it is served by a **CDN**, which answers the same name with an address near the person asking. Different location, different answer.

**P22** — **2^128**, about 3.4 × 10^38. It makes **NAT** unnecessary, because every device can have its own public address.

---

## One-page reference

**Your own network — find it**

| Command | Shows |
|---|---|
| `ip addr` | your addresses and masks |
| `ip route` | your default gateway |
| `curl ifconfig.me` | the **public** address the world sees |
| `dig <name>` | name → address, with TTL |
| `dig +short <name>` | just the address |
| `dig -x <address>` | address → name |
| `traceroute -n <name>` | every hop on the way |
| `sudo tcpdump -i any -n host <ip>` | your own packets |

**Reserved IPv4 ranges**

| Range | Purpose |
|---|---|
| `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` | **private** — not routed on the internet |
| `127.0.0.0/8` | loopback |
| `169.254.0.0/16` | link-local — **means DHCP failed** |
| `100.64.0.0/10` | carrier-grade NAT (the ISP's own private space) |
| `224.0.0.0/4` | multicast |
| `255.255.255.255` | broadcast |
| `192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24` | documentation only |

**Masks**

| Mask | Bits | Usable hosts |
|---|---|---|
| `255.255.255.0` | `/24` | 254 |
| `255.255.254.0` | `/23` | 510 |
| `255.255.0.0` | `/16` | 65,534 |

The decision, every time: `destination & mask == my address & mask` → **direct**, else → **gateway**.

**DHCP** — **D**iscover · **O**ffer · **R**equest · **A**cknowledge. Delivers address, mask, gateway, DNS and a lease time.

**NAT** — rewrites `private:port` → `public:newport` on the way out, and reverses it on the way back using a table keyed by the **outside port**. A row exists only if someone inside started the connection — which is why inbound connections fail and tunnels work.

**DNS chain** — your cache → `/etc/hosts` → resolver → root → TLD (`.com`) → authoritative. Answers cached for the **TTL**.

| Record | Holds |
|---|---|
| `A` / `AAAA` | IPv4 / IPv6 address |
| `NS` | authoritative servers |
| `MX` | mail servers |
| `CNAME` | alias |
| `PTR` | reverse lookup |

**traceroute** — sends TTL 1, 2, 3 … and maps the routers that complain. `*` means "no reply", not "blocked". Hop 1 is always your gateway.

**Who can see what**

| | Address & port | Hostname | Path & body |
|---|---|---|---|
| `http://` | everyone on the path | everyone | **everyone** |
| `https://` | everyone on the path | usually visible | encrypted |

WPA2/WPA3 protects **one hop only**. Open WiFi protects nothing.

**IPv4 vs IPv6**

| | IPv4 | IPv6 |
|---|---|---|
| Size | 4 bytes | 16 bytes |
| Count | 2^32 ≈ 4.3 × 10^9 | 2^128 ≈ 3.4 × 10^38 |
| NAT | necessary | unnecessary |
| Address setup | DHCP | SLAAC |
| Broadcast | yes | none — multicast instead |

**Rules to keep:**
- `192.168.x.x` is private. Millions share it; nothing outside can address it.
- AND with the mask: same network → direct, otherwise → the gateway.
- Your address is a **lease**, not a possession.
- NAT remembers connections by **port**, and only for connections started from inside.
- A tunnel works because the outbound connection comes first.
- DNS answers are **cached** for the TTL — that is why changes take time.
- Hop 1 is always your own gateway; `*` is silence, not failure.
- Geography is not topology. A name tells you nothing about where the server is.
- WiFi encryption covers one hop; only HTTPS covers the whole path.

---

## New Words (కొత్త పదాలు — తెలుగు అర్థాలు)

| English | తెలుగు | Meaning |
|---|---|---|
| IP address | ఐపీ చిరునామా | నెట్‌వర్క్‌లో ఒక యంత్రం చిరునామా — 4 బైట్‌లు |
| private address | ప్రైవేట్ చిరునామా | లోపలి నెట్‌వర్క్‌లోనే పనిచేసేది (`192.168.x.x`) |
| public address | పబ్లిక్ చిరునామా | ఇంటర్నెట్‌లో అందరికీ కనిపించేది |
| loopback | లూప్‌బ్యాక్ | యంత్రం తనతో తానే మాట్లాడేది — `127.0.0.1` |
| subnet | సబ్‌నెట్ | ఒక చిన్న నెట్‌వర్క్ ముక్క |
| subnet mask | సబ్‌నెట్ మాస్క్ | చిరునామాలో ఎంత భాగం నెట్‌వర్క్‌దో చెప్పేది |
| default gateway | డిఫాల్ట్ గేట్‌వే | బయటికి వెళ్ళే ద్వారం — రౌటర్ |
| broadcast | బ్రాడ్‌కాస్ట్ | నెట్‌వర్క్‌లో అందరికీ ఒకేసారి పంపడం |
| DHCP | డీహెచ్‌సీపీ | చిరునామాలను తానే పంచే సేవ |
| lease | లీజు | కొంతకాలానికే ఇచ్చిన చిరునామా |
| NAT | నాట్ | ప్రైవేట్ చిరునామాను పబ్లిక్‌గా మార్చడం |
| port | పోర్ట్ | ఒకే చిరునామాలో వేర్వేరు కనెక్షన్‌లను గుర్తించే సంఖ్య |
| tunnel | టన్నెల్ | బయటినుంచి లోపలికి చేరే దారి (ngrok) |
| DNS | డీఎన్‌ఎస్ | పేరును చిరునామాగా మార్చే వ్యవస్థ |
| resolver | రిజాల్వర్ | మన తరఫున DNS ప్రశ్నలు అడిగేది |
| TTL | టీటీఎల్ | జవాబును ఎంతసేపు దాచవచ్చో / ఎన్ని హాప్‌లో |
| authoritative server | అధికారిక సర్వర్ | ఆ డొమైన్ అసలు రికార్డు ఉన్న సర్వర్ |
| hop | హాప్ | దారిలో ఒక రౌటర్ |
| router | రౌటర్ | ప్యాకెట్‌ను తదుపరి దారికి పంపే యంత్రం |
| traceroute | ట్రేస్‌రూట్ | దారిలోని అన్ని హాప్‌లను చూపే ఆదేశం |
| backbone | వెన్నెముక | ఖండాల మధ్య ట్రాఫిక్ మోసే పెద్ద నెట్‌వర్క్ |
| packet sniffing | ప్యాకెట్ స్నిఫింగ్ | నెట్‌వర్క్‌లోని ప్యాకెట్‌లను చదవడం |
| CDN | సీడీఎన్ | దగ్గరలో కాపీలు ఉంచి వేగంగా ఇచ్చే వ్యవస్థ |
| anycast | ఎనీకాస్ట్ | ఒకే చిరునామా అనేక చోట్ల — దగ్గరిది జవాబిస్తుంది |
| IPv6 | ఐపీవీ6 | 16 బైట్‌ల కొత్త చిరునామా విధానం |
