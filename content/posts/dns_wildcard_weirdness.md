---
title: DNS wildcard weirdness
description: An interesting DNS issue
date: "2014-03-31"
categories:
    - "DNS"
    - "CloudFlare"
    - "Dokku"
---

It happens that I have a subdomain of one of my web domains bound to our home IP address, so that I can VPN into our home network, etc.

Our wifi router (an Asus model) had its domain name set to the same (i.e., `subdomain.thedomain.com`). So, the home router (which also handles DNS caching) was spitting out DNS configurations that looked like this:

    search subdomain.thedomain.com
    nameserver 8.8.8.8
    nameserver 192.168.12.254

Some of you who have experience with DNS and DHCP will probably start to guess what's going on here. I recently enabled DNS wildcards on the domain, in order to do some work with [Dokku](https://github.com/progrium/dokku) (post on that forthcoming). This means that every subdomain under `*.thedomain.com` gets automatically directed to `198.51.100.82`. Cool. But when the DHCP server for our house is pushing out `resolv.conf` entries containing that search directive, everything gets a bit ~~pear shaped~~ interesting.

In this circumstances, *any* non-existant hostname or subdomain requested from within my house resolves to `198.51.100.82`. *Any*.

| Test Case                   | Result          |
|-----------------------------|-----------------|
| `thedomain.com`             | `198.51.100.82` |
| `nonexistant.thedomain.com` | `198.51.100.82` |
| `nonexistant.com`           | `198.51.100.82` |
| `nonexistant.google.com`    | `198.51.100.82` |
| `dramatichatsforpandas.com` | `198.51.100.82` |

Removing the search domain worked. Didn't need it anyway.

**Verdict**: Good way to get a captive audience for my blog. *Weird* way to spend a Sunday afternoon.
