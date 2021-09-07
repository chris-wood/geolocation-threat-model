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

--- abstract

This document describes a threat model for geolocation services and interactions with users.

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

## Terminology

The following terms are used throughout this document.

Geolocation:
: The geographic location of an entity, e.g., geographic coordinates.

Anchor:
: An entity and geolocation source with a well-known and specific geolocation.

Relying Party (RP):
: An entity that wants to learn the geolocation of another entity.

User:
: The entity to which geolocation information typically pertains.

## Geolocation Use Cases

User geolocation has a number of critical use cases on the web, including, though not limited to:

- Content customization: Content varies in time and space. For example, an Internet
  search for “nearby restaurants” will necessarily return different results for
  different users depending on their location. Advertisements for content may also
  vary based on location. Advertisements in one region may not be available in another region.
  These use cases do not require absolute geolocation information, i.e., it may be accurate
  within some margin of error.
- Compliance: In some cases, content available in one region is not allowed to be available
  in another region, perhaps due to copyright restrictions or other regional reasons. User
  location is often used to infer these regions and enforce these rules. Popular streaming
  services, for example, apply geo fences based on user location. These use cases require
  absolute geolocation information, i.e., there is no margin of error in the information.
- Fraud and abuse detection: Many anti-bot detection and mitigation systems use user location,
  or proxies for location such as IP addresses, as a signal for bot detection behavior.
  Observing many suspicious connections coming from the same location is often, though not
  necessarily, indicative of malicious behavior.

Most of these use cases impact user experience in one way or another. Others go beyond user
experience and have legal or jurisdictional implications.

## Geoocation Sources

There are at least two classes of geolocation APIs available. A description of each follows.

- Client-side APIs: These include APIs such as CoreLocation, the W3C Geolocation API,
  and so on, which provide clients with a simple API for getting location. In most cases,
  these APIs are gated by user consent. That is, end-users must explicitly allow the API
  to reveal the user location.
- Online services: Many online services provide proprietary APIs for mapping an IP address
  to a location, along with other metadata about the IP address. Some of these services offer
  a free, rate-limited API, whereas others offer paid variants with more options. Unlike
  client-side APIs, servers generally have access to client IP addresses without user consent
  (absent proxying technologies such as Private Relay or Tor). Other variants depend on
  Anchors for the purposes of relative positioning.

The sources of truth for these geolocation APIs and services vary widely. They include, but
are not limited to GPS information, network signalling information (IP, RFID, WiFi and
Bluetooth MAC addresses), GSM/CDMA cellular network IDs, and even user input. In most cases,
the signals that feed into geolocation information are unauthenticated: there is no proof
that the output is actually correct or originated from a trusted source. Moreover, there
is no existing notion of a trusted source of geolocation information as there is no way
for sources to present proof of correctness or authenticity.

## Geolocation Properties

There are a number of properties one might consider with respect to geolocation information.
A description of some properties follows.

- Liveness or freshness: Geolocation information may be fresh, i.e., computed or otherwise
  determined on demand, or it may not be. Geolocation information that is not fresh can be
  used (or abused) by entities to influence their perceived location after having changed
  their actual geolocation information.
- Accuracy: Geolocation information be precise, i.e., pointing to a specific latitude and
  longitude, or more coarse, encompassing a larger area of physical distance.
- Consent: Geolocation may be dependent on user consent as explicit information sent to
  or revealed from one entity to another, or it may be implicit and inferred without consent.
- Confidence: Geolocation information correctness may be a random variable, varying in
  terms of how confident or correct the information may be.
- Composition: Geolocation information may be composed to produce new geolocation information.
  For example, two different geolocation sources may be combined to produce a single type
  of geolocation information.

# Threat Model

In most geolocation interactions, a Relying Party (server) wants to learn the location
of a user (client) for the purposes of addressing one or more of the use cases in
{{geolocation-use-cases}}. Depending on the implementation, clients may or may not
require user consent before revealing geolocation information to the server.

In this interaction, we consider the following server and source threat models:

1. Servers are malicious and want to try and learn precise client location or identity,
   even without consent from the user. This allows servers to interact with clients or
   any other entity as needed to subvert the user's desired privacy goals.
1. Some subset of geolocation sources (and APIs) are trusted, whereas others are assumed
   to be malicious. Indeed, some amount of geolocation sources must be assumed to be
   trustworthy for any use cases to work reliably.

Network characteristics between clients and servers such as latency and round trip time
are assumed untrusted. For example, clients can alter the round trip time by artificially
delaying packets. On-path attackers have the same capability.

In contrast, path characteristics such as routing are assumed trusted. Attacks which
subvert or otherwise modify path-layer information at the routing layer are technically
challenging to address. For example, clients may lie about their geolocation by tampering
with path characteristics, e.g., by changing egress IP address in server connections.
Likewise, attackers may interfere with BGP or other Internet routing to influence
geolocation services.

Servers cannot distinguish between honest and dishonest clients without additional
assumptions. Even with such a distinguisher, attacks that modify path characteristics
remain.

# Security Considerations {#sec-considerations}

This document describes a threat model for geolocation use cases, intended to help
discussions on this problem.

# IANA Considerations

This document makes no IANA requests.

--- back


