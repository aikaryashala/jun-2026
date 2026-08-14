# How a Packet Finds You — Question Bank

Answer on paper, using the worksheet's ideas: an IPv4 address is **four bytes**; `192.168.x.x` is **private** and unreachable from outside; the **mask** decides direct-or-gateway; **DHCP** leases the address; **NAT** remembers connections by **port**; **DNS** walks right to left; and **geography is not topology**.

Set your answers in the AI Karyashala network wherever the question does: LAN `192.168.0.0/24`, gateway `192.168.0.1`, one public address.

Answers are **not** in this file.

---

# Part A — Multiple Choice

**A1.** An IPv4 address is four bytes. How many addresses exist in total?

- A) 2^16
- B) 2^24
- C) 2^32
- D) 2^128

**A2.** `192.168.0.11` is:

- A) a public address, reachable from anywhere
- B) a private address, usable only inside a local network
- C) a loopback address
- D) a multicast address

**A3.** Which of these is **not** a private range?

- A) `10.0.0.0/8`
- B) `172.16.0.0/12`
- C) `192.168.0.0/16`
- D) `172.40.0.0/16`

**A4.** Your interface shows `169.254.7.9`. This means:

- A) you are on a fast network
- B) DHCP failed and the machine assigned itself an address
- C) you have a public address
- D) you are connected by cable, not WiFi

**A5.** `127.0.0.1` is used for:

- A) the default gateway
- B) broadcasting to the whole network
- C) the machine talking to itself
- D) the DNS server

**A6.** `/24` written as a dotted mask is:

- A) `255.255.0.0`
- B) `255.255.255.0`
- C) `255.255.255.24`
- D) `24.0.0.0`

**A7.** How many usable host addresses does a `/24` network have?

- A) 256
- B) 255
- C) 254
- D) 253

**A8.** Your machine is `192.168.0.11/24`. Sending to `192.168.3.50`, it will:

- A) send directly to that machine
- B) send it to the default gateway
- C) drop it
- D) broadcast it

**A9.** What operation does a machine use to decide whether a destination is on its own network?

- A) it compares the two addresses as text
- B) it ANDs both addresses with the subnet mask and compares
- C) it asks the DNS server
- D) it asks the DHCP server

**A10.** The four DHCP messages, in order, are:

- A) Request, Offer, Discover, Acknowledge
- B) Discover, Request, Offer, Acknowledge
- C) Discover, Offer, Request, Acknowledge
- D) Offer, Discover, Acknowledge, Request

**A11.** Why must the first DHCP message be a broadcast?

- A) broadcasts travel faster
- B) the machine has no address yet, so it cannot send an ordinary packet
- C) DHCP servers only accept broadcasts
- D) to tell every machine that a new one has joined

**A12.** Thirty students load the same website. How many source IP addresses does that website see?

- A) thirty
- B) one
- C) one per website visited
- D) it depends on the browser

**A13.** In a NAT table, the field that distinguishes two students' connections to the same site is the:

- A) destination address
- B) private address only
- C) outside port number
- D) MAC address

**A14.** A NAT row is created when:

- A) the router starts up
- B) a machine inside starts an outbound connection
- C) a packet arrives from the internet
- D) DHCP assigns an address

**A15.** Your friend on mobile data cannot reach `http://192.168.0.11:8000` because:

- A) the port is wrong
- B) the address is private and there is no NAT row for an unsolicited packet
- C) `http` is disabled on mobile networks
- D) the laptop firewall always blocks it

**A16.** ngrok gets past NAT because:

- A) it opens a port on the router automatically
- B) your machine makes an outbound connection first, and traffic returns down it
- C) it gives your laptop a public IP address
- D) it disables NAT temporarily

**A17.** In `codekaryashala.com. 1799 IN A 23.95.60.64`, the `1799` is:

- A) the port number
- B) the number of servers
- C) the TTL in seconds
- D) the record's serial number

**A18.** Which DNS record type maps a name to an IPv4 address?

- A) `MX`
- B) `NS`
- C) `A`
- D) `PTR`

**A19.** The correct order of DNS lookup is:

- A) authoritative → TLD → root → resolver
- B) resolver → root → TLD → authoritative
- C) root → resolver → authoritative → TLD
- D) resolver → authoritative → root → TLD

**A20.** In a traceroute from your laptop, hop 1 is always:

- A) your ISP
- B) the DNS server
- C) your default gateway
- D) the destination server

**A21.** A traceroute line shows `*`. This means:

- A) the packet was lost and the trace failed
- B) that router did not reply to the probe, but packets still pass through
- C) the destination was reached
- D) the network is down

**A22.** `traceroute` discovers each hop by:

- A) asking the router for its name
- B) sending packets with increasing TTL and reading the expiry errors
- C) reading the routing table of every router
- D) querying DNS for each address

**A23.** A packet from Vizag to a server in California passes through Paris. The reason is:

- A) a configuration mistake
- B) routing follows the cables and agreements between networks, not the map
- C) Paris is the closest city to California
- D) DNS chose the route

**A24.** In a college with one broadband line and thirty classrooms, NAT happens:

- A) once per classroom
- B) once in total, at the edge router
- C) once per machine
- D) never — classrooms use public addresses

**A25.** Each classroom is its own subnet mainly because:

- A) it is required by law
- B) it keeps broadcast traffic local and makes problems easier to isolate
- C) it makes the internet faster
- D) machines cannot share a subnet

**A26.** On AI Karyashala's WPA2 WiFi, can a neighbour read your traffic?

- A) yes, WiFi is radio so everything is visible
- B) no, each device has its own session key
- C) only if they use Wireshark
- D) only on a wired connection

**A27.** With HTTPS, an intermediate network can still see:

- A) your password
- B) the page contents
- C) who you connected to, when, and how much data moved
- D) nothing at all

**A28.** A modern switch differs from an old hub because it:

- A) is faster
- B) sends each frame only to the port of its destination
- C) encrypts all traffic
- D) assigns IP addresses

**A29.** The main purpose of a CDN is to:

- A) store your passwords securely
- B) serve content from a location near the user, cutting latency and origin load
- C) replace DNS
- D) provide public IP addresses

**A30.** IPv6 addresses are 16 bytes. How many are there?

- A) 2^32
- B) 2^64
- C) 2^128
- D) 2^256

**A31.** The IPv6 mechanism that makes NAT unnecessary is:

- A) faster routing
- B) having enough addresses for every device to have a public one
- C) built-in encryption
- D) shorter headers

---

# Part B — Fill in the Blanks

**B1.** An IPv4 address is ____________ bytes, each holding a number from ____________ to ____________.

**B2.** The three private ranges are ____________, ____________ and ____________.

**B3.** The address a machine gives itself when DHCP fails begins with ____________.

**B4.** `/24` means the first ____________ bits are the ____________ part of the address.

**B5.** To decide whether to send a packet directly, a machine ____________ the destination with the subnet mask and compares it with its own network.

**B6.** Anything not on the local network is sent to the ____________ ____________.

**B7.** The four DHCP steps are ____________, ____________, ____________ and ____________.

**B8.** An address given by DHCP is a ____________, which expires after a set time.

**B9.** NAT stands for ____________ ____________ ____________, and it tells connections apart using the ____________ number.

**B10.** A NAT entry is created only when a machine ____________ the network starts a connection.

**B11.** In a DNS answer, the number before `IN A` is the ____________, measured in ____________.

**B12.** DNS is resolved from ____________ to ____________ across the name: first the root, then the ____________, then the ____________ server.

**B13.** `traceroute` works by sending packets with an increasing ____________ and collecting the errors sent back.

**B14.** A network run by a single organisation, with its own number, is called an ____________ ____________.

**B15.** WiFi encryption protects ____________ hop, while ____________ protects the contents all the way to the server.

**B16.** A CDN sends a user to a nearby copy either by answering DNS differently per region, or by giving many servers the same address, which is called ____________.

---

# Part C — Scenario Questions

**C1.** Three students run `ip addr` and see `192.168.0.11`, `192.168.0.17` and `192.168.0.23`, all with `/24`.
(a) What do they have in common, and what does that tell you?
(b) All three then run `curl ifconfig.me`. What will they see, and why?
(c) Reconcile (a) and (b): how can three machines with three addresses appear to the world as one?

**C2.** A student's laptop shows `169.254.202.14` and nothing works.
(a) What is this address and where did it come from?
(b) Give two different reasons this could have happened.
(c) What would you check first?

**C3.** Your machine is `192.168.0.11/24`, gateway `192.168.0.1`. For each destination, say whether the packet goes direct or to the gateway, and show the AND that decides it: (a) `192.168.0.1` (b) `192.168.0.200` (c) `192.168.1.11` (d) `8.8.8.8`

**C4.** AI Karyashala's router leases `192.168.0.100` to `192.168.0.200`.
(a) How many devices can be connected at once?
(b) A workshop day brings 130 devices. What happens to the last few, and what will their users see?
(c) Give two ways to fix it, and say which you would choose.

**C5.** Ravi opens github.com, Asha opens wikipedia.org, Balaji opens codekaryashala.com — all at the same instant, all through one router with public address `49.207.41.133`.
(a) Draw the NAT table with a row per student.
(b) A reply arrives addressed to `49.207.41.133:60002`. Trace exactly what the router does with it.
(c) Ravi and Balaji both happened to use source port `51001` on their own machines. Why is that not a problem?

**C6.** A student runs `python3 -m http.server 8000` and sends a classmate the link `http://192.168.0.11:8000`. It works for a classmate in the room but not for the student's brother at home.
(a) Why does it work in the room?
(b) Why does it fail from outside? Answer in terms of the NAT table.
(c) Describe the fix and explain why it succeeds where opening the link did not.

**C7.** `dig codekaryashala.com` is run twice, ten seconds apart. The first shows TTL 1799, the second 1789.
(a) What is happening to that number?
(b) Where is the answer being kept in the meantime?
(c) The site's owner changes the server's address. Some students see the new site immediately and others do not. Explain, and say what governs how long the difference lasts.

**C8.** A traceroute shows hops 1–4 inside India with times of 3–6 ms, then `* * *` for hops 7–12, and hop 13 at 154 ms.
(a) What are the `*` lines, and did the packets get through?
(b) What most likely accounts for the jump from 6 ms to 154 ms?
(c) A classmate concludes "the network is broken between hops 7 and 12". Are they right?

**C9.** `codekaryashala.com` is described as being in Amalapuram, 200 km from Vizag. A traceroute goes Vizag → Pune → Paris → Ashburn → San Jose, and `dig -x` returns a `colocrossing.com` name.
(a) Where is the server really?
(b) What does a domain name tell you about a server's location?
(c) Explain why a packet heading to America goes via Paris.
(d) What single change would cut the 346 ms round trip for students, and what is it called?

**C10.** A college has classrooms on `192.168.4.0/24`, `192.168.5.0/24` and `192.168.6.0/24`, a core switch, and one edge router with public address `203.0.113.7`.
(a) A machine in room 4 sends to a machine in room 5. Describe the path and say whether NAT is involved.
(b) The same machine loads a website. Describe the path now, and say where NAT happens.
(c) A reply arrives at `203.0.113.7:61001`. Explain the two lookups that deliver it to room 4, machine 12.
(d) Why not put all 600 machines on one `192.168.0.0/16` network instead?

**C11.** A student says: "Our WiFi has a password, so our traffic is safe all the way to the website."
(a) Which part is correct?
(b) Which part is wrong, and why?
(c) The same student uses an airport's open WiFi and logs into a site over `http://`. What exactly is exposed, and to whom?
(d) What single change would have protected them?

**C12.** `dig +short www.wikipedia.org` returns a `CNAME` and then an address. A friend in London runs the same command and gets a different address.
(a) Why do the answers differ?
(b) Name the two mechanisms a CDN can use to do this.
(c) Give three reasons a site would want this, beyond speed.

**C13.** A team argues about whether to adopt IPv6.
(a) How many addresses does IPv6 provide, and how does that compare with IPv4?
(b) Name three things it changes besides the number of addresses.
(c) If it is better, why is IPv4 still everywhere? Name the practical obstacle.
(d) Which capability matters most to someone building a mobile app that needs devices to talk to each other, and why?

---

# Part D — Read the Output

**D1.** From this output, state the machine's address, its mask in dotted form, its network address, its broadcast address, and its default gateway.
```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.0.11/24 brd 192.168.0.255 scope global eth0

default via 192.168.0.1 dev eth0
192.168.0.0/24 dev eth0 proto kernel scope link src 192.168.0.11
```

**D2.** For the machine in D1, say direct or gateway for each: `192.168.0.1`, `192.168.0.255`, `192.168.10.5`, `127.0.0.1`, `23.95.60.64`.

**D3.** Read this answer and state the name asked for, the record type, the TTL, the address, and which server answered.
```
;; ANSWER SECTION:
codekaryashala.com.	1799	IN	A	23.95.60.64

;; Query time: 73 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
```

**D4.** What does each command ask for?
(a) `dig +short NS codekaryashala.com`
(b) `dig -x 23.95.60.64`
(c) `dig AAAA www.google.com`
(d) `dig MX gmail.com`

**D5.** From this trace, name the hop that is your own router, the last hop belonging to your ISP, the first hop belonging to a different company, and the two hops using private addresses.
```
 1  192.168.0.1                              3.351 ms
 2  10.192.0.1                               5.359 ms
 3  106.51.40.153                            5.362 ms
 4  broadband.actcorp.in (49.207.41.133)     5.979 ms
 5  115.113.104.117.static-pune.vsnl.net.in  5.816 ms
 6  172.25.81.134                           28.338 ms
```

**D6.** These hostnames appeared later in the same trace. Name the city each is in, and say what the whole sequence tells you about the route.
```
14  prs-bb2-link.ip.twelve99.net    173.762 ms
15  ash-bb2-link.ip.twelve99.net    278.682 ms
16  ewr-bb2-link.ip.twelve99.net    271.275 ms
20  palo-bb4-link.ip.twelve99.net   269.023 ms
22  sjo-bb3-link.ip.twelve99.net    345.965 ms
```

**D7.** Complete this NAT table. The router's public address is `49.207.41.133`.

| Outside | Inside | Destination |
|---|---|---|
| `49.207.41.133:60001` | `192.168.0.11:51001` | `140.82.121.4:443` |
| `49.207.41.133:60002` | ? | `103.102.166.224:443` |

A reply arrives for `49.207.41.133:60002`. Which machine gets it, and to which port?

**D8.** For each address say private, public, loopback, link-local or multicast: `10.1.1.1` · `172.31.5.5` · `172.32.5.5` · `192.168.1.1` · `127.0.0.1` · `169.254.5.5` · `224.0.0.1` · `49.207.41.133`

**D9.** Give the usable host count for `/24`, `/23`, `/16` and `/8`.

**D10.** For each, state whether it is visible to an intermediate network over `http://`, and over `https://`: the destination address · the hostname · the URL path · a submitted password · the response body.
