## 4. Description of Frames used in WP1

### 4.1. Purpose and Overview

This chapter provides a detailed description of each NeTEx frame used in the CFL profile for the MVP.  
While the previous chapter explained how data are organized and exchanged across files, this section focuses on the internal structure and purpose of each frame within the dataset.

Each frame is described with its **scope**, **key entities**, **dependencies**, and **level of implementation** in the MVP.  
Common modelling principles applicable to all frames are summarized at the end of this chapter, before moving on to the interactions between them.

---

### 4.2. ResourceFrame

#### Purpose and scope

The **ResourceFrame** gathers the reference data and common reusable definitions used by all other frames.  
It defines organizations, operators, roles, and various classification types that provide context and identifiers for other datasets.

In the CFL NeTEx profile, the ResourceFrame acts as the **core reference layer**, ensuring consistent identifiers and terminology across Site, Service, and Timetable frames.

#### Key entities included in WP1

| Entity                           | Definition                                                | Typical usage                                                         | Mandatory (MVP) |
|----------------------------------|----------------------------------------------------------|------------------------------------------------------------------------|-----------------|
| Organisation                     | Legal or administrative structure involved in transport operations. | Defines CFL and other operators participating in the multimodal dataset. | ✅ |
| Operator                         | Organisation responsible for operating services.         | Used as reference in Line and ServiceJourney elements.                | ✅ |
| Authority                        | Public transport authority managing the network or lines.| Defines the regulatory context (e.g. Ministry of Mobility).           | ✅ |
| ResponsibilityRoleAssignment     | Allocation of a role (e.g., Operator, DataProvider).     | Links organisations to their functions.                               | ✅ |
| TypeOfFrame / TypeOfValue / TypeOfEntity | Classification or metadata definitions.           | Defines controlled vocabularies for modes, service types, etc.        | Optional |
| UnitsOfMeasure, Currency, CountryRef | Common reference data.                            | Shared across frames for standardisation.                             | Optional |

#### Dependencies

- All other frames (*SiteFrame*, *ServiceFrame*, *TimetableFrame*) reference entities defined here.  
- Identifiers must use the **CFL codespace** (`LU:CFL:` prefix) and remain stable over time to ensure traceability and versioning consistency.

#### MVP scope

In the MVP, the ResourceFrame includes only the **core entities** necessary for timetable publication (Operator, Organisation, Authority, ResponsibilityRoleAssignment).  
Extended reference data (e.g., detailed responsibilities, value sets, or external organisations) will be included in later extensions.

#### Future extensions

- **WP3 (Accessibility):** add accessibility classification types and mobility aids.  
- **WP4 (Tariff):** add fare structures, value sets for tariffs and currencies.  
- **WP5 (New modes):** add additional operators or authority references for emerging modes.

---
### 4.3. SiteFrame

#### 4.3.1. Purpose and Scope

The SiteFrame describes the physical topology of the network: all places where passengers can access or leave the transport system.  
It models stops, quays, entrances, accesses, and their relationships, and defines how these places are organised into larger complexes such as stations or multimodal hubs.

In CFL’s NeTEx profile, the SiteFrame serves as the **geographical and spatial reference layer** for all journeys.  
Each StopPlace and Quay defined here provides the physical anchor points referenced later in the ServiceFrame and TimetableFrame.


#### 4.3.2. Key Entities Included in WP1

| Entity               | Definition                                                               | Typical usage                                           | Mandatory (MVP) |
|----------------------|---------------------------------------------------------------------------|----------------------------------------------------------|-----------------|
| StopPlace            | A named place where passengers can board or alight. May include multiple quays. | Represents CFL stations and multimodal interchanges. | ✅ |
| Quay                 | A physical boarding area or platform associated with a StopPlace.        | Used in StopPointInJourneyPattern references.          | ✅ |
| AccessSpace / Entrance | Access zones and entrances to the StopPlace.                          | Optional in WP1; foreseen for accessibility extensions.| ❌ |
| TopographicPlace     | Geographic reference (city, area) associated with the StopPlace.         | Optional in WP1; Enables geographical grouping of stops.                | ❌ |
| PlaceEquipment / Parking / BikeParking | Facilities associated with stops.                     | Out of WP1 scope; to be included in WP3.               | ❌ |
| GroupOfStopPlaces    | Logical grouping (e.g. multimodal interchange).                          | Will be used for complex sites with multiple modes.    | ❌ |


#### 4.3.3. Transfer and Connection Modelling (Transfer Times / “Temps de battement”)

The SiteFrame also hosts the description of **intra-site transfer possibilities** — the time needed for passengers to walk between two boarding areas or to connect between services within the same station.

These durations, sometimes called *minimum transfer times* or *temps de battement*, are modelled in NeTEx using **Connection** or **Transfer** elements, located inside the SiteFrame.

| Element                           | Definition                                                                 | Typical usage                                                                 | Mandatory (MVP) |
|-----------------------------------|---------------------------------------------------------------------------|-------------------------------------------------------------------------------|------------------|
| Connection / Transfer             | Logical link between two StopPlaces or two Quays, with an associated transfer duration. | Used to express the minimum interchange time between platforms or services within the same station. | ✅ (at StopPlace level) |
| TransferDuration                  | Duration of the connection, expressed as ISO 8601 duration.              | Represents the battement known in SIV for each station.                       | ✅ |
| MobilityRestrictedTravellerDuration | Alternative duration for passengers with reduced mobility.             | To be added in WP3 (Accessibility).                                           | ❌ |

Additional modelling rules:

- In WP1, transfer durations are defined **at StopPlace level** (not per Quay), as CFL systems (e.g., SIV) provide one battement per commercial stop.  
- Each Connection or Transfer shall link a **StopPlace to itself** (`FromStopPlaceRef = ToStopPlaceRef`), with the applicable duration.  
- The attribute `TransferDuration` is expressed in **ISO 8601 format** (e.g., `PT5M` = 5 minutes).  
- More detailed modelling between individual Quays may be introduced in WP3 when accessibility data are included.


#### 4.3.4. XML Example

```xml
<SiteFrame id="LU:SITE:FRAME:1" version="1">
  <connections>
    <Connection id="LU:CONNECTION:NOERTZANGE_INTRA" version="1">
      <From>
        <StopPlaceRef ref="LU:STOPPLACE:NOERTZANGE"/>
      </From>
      <To>
        <StopPlaceRef ref="LU:STOPPLACE:NOERTZANGE"/>
      </To>
      <TransferDuration>PT5M</TransferDuration>
    </Connection>
  </connections>
</SiteFrame>
```

This Connection expresses that within the station Noertzange, passengers need at least 5 minutes to transfer between trains.  
This value corresponds to the *temps de battement* maintained in SIV.

#### Dependencies

- Referenced by **ServiceFrame** (through *StopPointInJourneyPattern → QuayRef*).  
- May reference entities in **ResourceFrame** (Operator, Authority).  
- Coordinates, identifiers, and names must remain stable across updates to ensure interoperability with SIRI and downstream systems.

#### MVP scope

WP1 focuses on:

- StopPlace and Quay objects for the national rail network.  
- Core attributes (identifier, name, coordinates, StopPlaceType, PublicCode).  
- Optional multilingual names (FR, DE, EN, LU) when available.  

Other detailed structures (Accessibility, Facilities, Equipment) are deferred to future work packages.

#### Future extensions

- **WP3:** accessibility attributes (lifts, tactile guidance, mobility aid access).  
- **WP4:** integration with fare zones and tariff references.  
- **WP5:** expansion to new modes (bus, tram, bike-box, park&ride sites).


### 4.4. ServiceFrame

#### Purpose and scope

The **ServiceFrame** contains the operational definition of the public-transport offer, including Lines, JourneyPatterns, StopPointsInJourneyPattern, and related entities.

In the Luxembourg WP1 profile, the ServiceFrame is the central frame describing:

- the logical structure of the timetable offer,  
- the sequence of stops served,  
- the public identity of services (*Line*),  
- the terminus and via information,  
- the references used later in the ServiceCalendarFrame and TimetableFrame.

Since **no NetworkFrame is used in WP1**, the ServiceFrame is also the place where **Lines are defined**, following the specific modelling principles of the CFL profile.

#### Key entities included in WP1

| Entity                     | Definition                                         | Typical usage                                             | Mandatory (MVP) |
|----------------------------|-----------------------------------------------------|------------------------------------------------------------|-----------------|
| Line                       | Public axis defined by an origin–destination pair. | Provides public identity and service classification for all JourneyPatterns and ServiceJourneys. | ✅ |
| JourneyPattern             | Ordered sequence of stop points describing the service pattern. | Used as reference in ServiceJourney. | ✅ |
| StopPointInJourneyPattern  | Specific stop in the pattern, pointing to a Quay. | Defines stop sequence for each pattern. | ✅ |
| DestinationDisplayRef      | Reference to destination display (if used).        | Optional. | ❌ |


#### Modelling of Lines in the CFL profile

The CFL profile defines **Line** as:

> A public axis represented by an ordered pair of major stops, identified using UIC origin and destination codes (optionally with “via”).

The Line is **not** a container for route geometry and does **not** define multiple routes.

Therefore:

- A Line does **not** reference `Route` / `RoutePoint` / `PointOnRoute`, as no NetworkFrame is used.  
- A Line acts as a **classification** and **public identity**.  
- All service topology is expressed only through **JourneyPatterns**.

**Example:**

- **Line Name:** Luxembourg – Arlon  
- **Line ID:** `LU:CFL:Line:0800_0840` (example using UIC codes)  
- The RB/RE/IC information is **not** included in `Line`; it is a *ServiceClassification* of the *ServiceJourney*.

---

#### Dependencies

- **SiteFrame**, for Quay references used in StopPointInJourneyPattern  
- **ResourceFrame**, for Operator, Organisation, ResponsibilityRole  
- **ServiceCalendarFrame**, indirectly, via the TimetableFrame  

---

#### MVP scope

WP1 defines a limited set of **JourneyPatterns per Line**, with StopPointsInJourneyPattern referencing Quays in SiteFrame.  
Only elements required to describe a **regular scheduled service** are included.

---

#### Future extensions

- **WP3:** accessibility data associated with JourneyPatterns.  
- **WP4:** fare-relevant references (e.g. TariffZones).  
- **WP5:** multi-modal patterns and flexible services.

#### 4.X. XML Example (ServiceFrame)

```xml
<ServiceFrame id="LU:SERVICE:FRAME:1" version="1">

  <!-- Line: public axis Luxembourg – Arlon -->
  <lines>
    <Line id="LU:CFL:Line:0800_0840" version="1">
      <Name>Luxembourg – Arlon</Name>
      <PublicCode>0800_0840</PublicCode>
      <OperatorRef ref="LU:OPERATOR:CFL"/>
    </Line>
  </lines>

  <!-- JourneyPattern: sequence of stops for the service -->
  <journeyPatterns>
    <JourneyPattern id="LU:JP:0800_0840:1" version="1">
      <Name>Luxembourg – Arlon Pattern</Name>
      <pointsInSequence>
        <StopPointInJourneyPattern id="LU:SPJP:LUX:1" order="1" version="1">
          <ScheduledStopPointRef ref="LU:SSP:LUXEMBOURG:Q1"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:SPJP:NOERTZANGE:2" order="2" version="1">
          <ScheduledStopPointRef ref="LU:SSP:NOERTZANGE:Q1"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:SPJP:ARLON:3" order="3" version="1">
          <ScheduledStopPointRef ref="LU:SSP:ARLON:Q1"/>
        </StopPointInJourneyPattern>
      </pointsInSequence>
    </JourneyPattern>
  </journeyPatterns>

</ServiceFrame>
```

