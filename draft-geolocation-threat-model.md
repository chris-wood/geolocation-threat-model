---
title: A Geolocation Threat Model
abbrev: Geolocation Threat Model
docname: draft-geolocation-threat-model-latest
date:
category: info

ipr: trust200902
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
 -  ins: C. A. Wood
    name: Christopher A. Wood
    org: Cloudflare
    email: caw@heapingbits.net

informative:
  XXX

--- abstract

This document describes a threat model for geolocation services.

--- middle

# Introduction

Many online services perform geolocation using IP-based databases. In general, such databases
map IP addresses to geographic locations. Most client devices also offer APIs for retrieving
geographic locations. Providers of these databases and APIs often combine multiple, and sometimes
private or privileged, sources of information for the mappings. Moreover, the details of mapping
construction is proprietary.

One aspect these services do have in common is that the mapping lacks authenticity. In particular,
neither online services nor client APIs provide any assurance that the location output is correct.
This is problematic considering that such location information is often used for critical applications,
including security and policy enforcement, content customization, and advertisements.

Addressing these shortcomings requires clarity on the problems being solved by geolocation mechanisms.
Moreover, for those problems, it is important to identify the threat model describing the behavior
of different system entities. This document is an attempt to start the discussion on these topics.

# Requirements Notation

{::boilerplate bcp14}

# Geolocation Background

This section describes use cases for geolocation services and sources
for this information.

## Use Cases

User location has a number of critical use cases on the web, including, though not limited to:

- Content customization: Content varies in time and space. For example, an Internet 
  search for “nearby restaurants” will necessarily return different results for 
  different users depending on their location. Advertisements for content may also 
  vary based on location. Advertisements in one region may not be available in another region.
- Compliance: In some cases, content available in one region is not allowed to be available 
  in another region, perhaps due to copyright restrictions or other regional reasons. User 
  location is often used to infer these regions and enforce these rules. Popular streaming 
  services, for example, apply geo fences based on user location.
- Fraud and abuse detection: Many anti-bot detection and mitigation systems use user location,
  or proxies for location such as IP addresses, as a signal for bot detection behavior. 
  Observing many suspicious connections coming from the same location is often, though not 
  necessarily, indicative of malicious behavior.

Most of these use cases impact user experience in one way or another. Others go beyond user 
experience and have legal or jurisdictional implications.

## Geoocation Sources

There are at least two classes of geolocation APIs available:

- Client-side APIs: These include APIs such as CoreLocation, the W3C Geolocation API, 
  and so on, which provide clients with a simple API for getting location. In most cases, 
  these APIs are gated by user consent. That is, end-users must explicitly allow the API 
  to reveal the user location. 
- Server-side APIs: The most popular server-side API is likely MaxMind, which provides 
  a proprietary and closed API for mapping an IP address to a location, along with other 
  metadata about the IP address. Some of these services offer a free, rate-limited API, 
  whereas others offer paid variants with more options. Unlike client-side APIs, servers 
  generally have access to client IP addresses without user consent (absent proxying 
  technologies such as Private Relay or Tor).

The sources of truth for these geolocation APIs vary widely. They include, but are not 
limited to GPS information, network signalling information (IP, RFID, WiFi and Bluetooth 
MAC addresses), GSM/CDMA cellular network IDs, and even user input. In most cases, the 
signals that feed into geolocation information are unauthenticated: there is no proof 
that the output is actually correct or originated from a trusted source. Moreover, there 
is no existing notion of a trusted source of geolocation information as there is no way 
for sources to present proof of correctness or authenticity.

# Threat Model and Problem Statement

In most geolocation interactions, a relying party (server) wants to learn the location
of a user (client) for the purposes of addressing one or more of the use cases in
{{use-cases}}. In this setting, we consider the following threat model:

1. Servers are malicious and want to try and learn precise client location or identity,
   even without consent from the user.
1. Some subset of geolocation sources (and APIs) are trusted, whereas others are assumed 
   to be malicious. Indeed, some amount of geolocation sources must be assumed to be
   trustworthy for any use cases to work reliably. 
1. Clients are honest. Dishonest clients can lie about their geolocation information, either
   by sending fake information to the server, or by otherwise modifying what information the
   server sees, e.g., by changing egress IP address in server connections. Servers cannot 
   distinguish between trusted and untrusted clients without additional assumptions.

Given this threat model and interaction, we ask the following question: Can we design a 
geolocation solution that provides authentic and verifiable geolocation signals?

# Security Considerations {#sec-considerations}

This document describes a threat model for geolocation use cases, intended to help
discussions on this problem.

# IANA Considerations

This document makes no IANA requests.

--- back
