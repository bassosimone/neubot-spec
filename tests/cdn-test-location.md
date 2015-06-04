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

## Example output

```json
{
  "input": [
    "www.google.com",
    "www.kernel.org",
    "www.123456.org"
  ],
  "dns_servers": [
    "8.8.8.8",
    "8.8.4.4",
    "208.67.222.222",
    "208.67.222.220",
    "default"
  ],
  "output": {
    "default_nameserver": "130.192.3.21",
    "8.8.8.8": {
      "www.google.com": {
        "ipv4": {
          "74.125.232.145": {
            "reverse": [
              "mil02s05-in-f17.1e100.net"
            ],
            "traceroute": "traceroute to 74.125.232.145 (74.125.232.145)...",
            "whois": "#\n# ARIN WHOIS\n...",
          },
          "74.125.232.146": {}
        },
        "ipv6": {
          "2a00:1450:4002:803::1010": {}
        }
      }
    },
    "8.8.4.4": {},
    "208.67.222.222": {},
    "208.67.222.220": {},
    "default": {}
  }
}
```

Alternative (probably better?) approach:

```json
{
  "input": [
    "www.google.com",
    "www.kernel.org",
    "www.123456.org"
  ],
  "dns_servers": [
    "8.8.8.8",
    "8.8.4.4",
    "208.67.222.222",
    "208.67.222.220",
    "default"
  ],
  "output": {
    "default_nameserver": "130.192.3.21",
    "8.8.8.8": {
      "A": {
        "www.google.com": [
          "74.125.232.145",
          "74.125.232.146"
        ],
        "www.kernel.org": []
      },
      "AAAA": {
        "www.google.com": [
          "2a00:1450:4002:803::1010"
        ],
        "www.kernel.org": []
      },
      "ReverseA": {
        "74.125.232.145": [
          "mil02s05-in-f17.1e100.net"
        ]
      },
      "ReverseAAAA": {
        "2a00:1450:4002:803::1010": [
          "mil02s05-in-f17.1e100.net"
        ]
      },
    }
    "traceroute": {
      "74.125.232.145": "traceroute to 74.125.232.145 (74.125.232.145)..."
    },
    "whois": {
      "74.125.232.145": "#\n# ARIN WHOIS\n..."
    },
  }
}
```
