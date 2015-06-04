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
  - Result of querying "whoami.akamai.net" (see below) 
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

The algorithm is straightforward considering the structure of the output presented above. Let us just clarify that querying "whoami.akamai.net" using the default resolver returns the host that executed the query that possibly is not a resolver, but it hints at who is running that server (recipe [lifted from serverfault](http://superuser.com/questions/536238/is-it-possible-to-find-out-which-upstream-dns-server-my-router-is-querying-jus)).
