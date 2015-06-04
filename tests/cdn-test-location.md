# CDN test location

- Author: Daniele Di Carlo and Simone Basso
- Version: 1.0
- Date: 2015-04-24
- Documents: CDN test

This document describes an idea for a test useful for locating
hosts belonging to content delivery networks.

A complementary problem is how to figure out which are the
most frequently contacted host, but this is not addressed
by this document, as this can be done when postprocessing data.

## High-level algorithm

Here we describe the input and output of the algorithm, as well
as the capabilities that the software must implement.

- Input:
  - List of domain names to be tested
  - List of DNS servers
- Output:
  - For each DNS server:
    - For each domain name:
      - Lookup IPv4
      - Lookup IPv6
    - For each IPv4 address:
      - Lookup reverse
      - Traceroute
      - Whois (?)
    - For each IPv6 address:
      - Lookup reverse
      - Traceroute
      - Whois (?)
- Capabilities:
  - DNS
    - Set specific DNS server
    - Use default DNS server
    - Find out system-wide DNS server
    - Resolve A, AAAA
    - Perform reverse lookups
  - Traceroute
    - Run traceroute or execute system-wide client
  - Whois 
    - Run whois or execute system-wide client

The following is the algorithm:

1. Find out the user's ISP default DNS
2. Add this DNS to the list of DNS servers
3. For each DNS server:
    1. For each domain name:
        1. ipv4_addr = Lookup(A, domain name)
        2. ipv6_addr = Lookup(AAAA, domain name)
        3. ReverseLookup(A, ipv4_addr)
        4. ReverseLookup(AAAA, ipv6_addr)
        5. Traceroute(ipv4_addr)
        6. Traceroute6(ipv6_addr)
        6. Whois(ipv4_addr)
        7. Whois(ipv6_addr)
