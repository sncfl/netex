# 1 - Overview

## 1.1 Context

Unlike several neighbouring countries, Luxembourg does not yet have a national profile for describing and exchanging public transport data. 
At the same time, European initiatives are converging on NeTEx and SIRI, which are the CEN standards for passenger information and data interoperability. 
These standards are increasingly used across Europe to ensure that timetable, network and real-time information can be exchanged consistently between systems and operators.

To contribute to this European dynamic and prepare the ground for a future national profile, CFL (Chemins de Fer Luxembourgeois) is publishing an initial Minimum Viable Profile (MVP) in NeTEx. This MVP focuses on CFL’s own rail timetable offer and serves two purposes:

- to provide an immediately usable foundation for describing and publishing CFL’s data,
- to act as a first milestone towards broader harmonisation that could later be extended to other operators and modes of transport in Luxembourg.

The approach is progressive and collaborative:

- the MVP is openly published (via GitHub),
- it can be iteratively refined and enriched,
- future evolutions are expected to involve cooperation with other mobility stakeholders in Luxembourg.

By launching this MVP, CFL set in motion a process that is both aligned with European standards and open to national collaboration, with the long-term vision of a shared Luxembourg NeTEx profile.

---

## 1.2 Objectives of the profile

The purpose of this profile is to define, in a clear and pragmatic way, how CFL timetable data should be structured and published using the European NeTEx standard.

The main objectives are:

- Provide a usable baseline : deliver a Minimum Viable Profile (MVP) that enables the publication of CFL’s rail timetable data in a structured, interoperable format.
- Ensure European alignment : comply with the NeTEx and Transmodel standards to guarantee interoperability with neighbouring countries and with European mobility initiatives.
- Support open data: make reliable timetable data available for reuse by travellers, developers, and institutional stakeholders.
- Promote internal consistency : establish a common reference within CFL, ensuring stability and avoiding diverging formats.
- Prepare for multimodality : design the profile in such a way that it can later be expanded to cover other transport modes (bus, tram, shared mobility) and additional functional domains (real-time, accessibility, fares).
- Foster collaboration : publish the profile openly (e.g. via GitHub) to encourage contributions from other operators and mobility stakeholders in Luxembourg.

In summary, this MVP aims to be both usable immediately and flexible enough to grow, forming the cornerstone of a broader, collaborative NeTEx profile for Luxembourg.

---

## 1.3 Target audience

This document is intended for a broad audience, ranging from technical experts to non-specialists who need to understand or reuse timetable data.

It addresses in particular:

- CFL internal teams : business analysts, data managers, system architects, developers.
- Other mobility operators in Luxembourg : who may later align with or contribute to the profile.
- Public authorities and institutional partners : ministries, municipalities, and transport agencies interested in interoperable data standards.
- Third-party developers and service providers : who will use open data to build journey planners, passenger information systems, or mobility apps.
- European stakeholders : to ensure compatibility and cross-border interoperability with neighbouring countries.

The profile is therefore written in a progressive and pedagogical way: it starts with general principles that can be understood by non-specialists, and gradually introduces the technical details needed by data and IT experts.

---

## 1.4 Scope of the initiative

The CFL profile will be built progressively in several work packages, each covering a functional perimeter.

- **WP 1 – NeTEx Timetable profile**  
  Covers the core static offer : stops, network, timetable, service calendars, and common elements. This MVP focuses initially on CFL’s rail offer, but is designed to be multimodal from the outset.

- **WP 2 – SIRI profile**  
  Covers real-time information through SIRI services (Estimated Timetable – ET, Situation Exchange – SX, Vehicle Monitoring – VM).

- **WP 3 – NeTEx Accessibility extension**

- **WP 4 – NeTEx Fare extension**

- **WP 5 – NeTEx & SIRI extensions for new modes**  
  Extends the profile to new mobility services (e.g. bikebox parking, Flex car-sharing).  
  (Work on this WP is expected only from late 2025 onwards, depending on progress in the French profile and other European references.)

---

## 1.5 Focus of WP1

WP1 establishes the core NeTEx timetable profile for CFL and applies to all CFL-operated modes that run on a published timetable, namely: rail, bus, funicular, and autonomous shuttles (and any additional CFL modes introduced with scheduled services).

It defines how to structure and publish the static offer across these modes, including:

- **Site elements :** stop places, quays/platforms  
- **Service elements :** lines, journey patterns  
- **Timetables :** planned services (VehicleJourney) and passing times  
- **Service calendars :** operating days, day types, and validity periods  
- **Common resources :** organisations, operators, codespaces, shared definitions  

### Scope boundaries (WP1)

- **Included :** multimodal coverage of CFL-operated services; modelling choices and identifiers harmonised across modes; cross-mode references and correspondences within CFL.
- **Excluded :** real-time information (SIRI), detailed accessibility data, and fares/ticketing structures — these are addressed in subsequent work packages.
- **Out of scope for WP1 :** services operated by non-CFL operators (which may be aligned and integrated later through the open, collaborative process).

WP1 is an MVP : immediately usable by CFL producers and consumers of data, while forming a stable foundation for later extensions and external contributions.
