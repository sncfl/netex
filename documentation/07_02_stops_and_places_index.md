# 7.2 Stops and places entities

## Scope (CFL MVP)

This section documents both:
- **physical stop infrastructure** defined in `stop.xml` (`StopPlace`, `Quay`), and
- the **logical stop model and mapping** used by line files (`ScheduledStopPoint`, `PassengerStopAssignment`).

Although `ScheduledStopPoint` and `PassengerStopAssignment` are defined in the **ServiceFrame of `line_<LineId>.xml`**, they are documented here because they define the boundary between **logical service stops** and **physical stop infrastructure**.

---

## Structure

1. [StopPlace (SiteFrame)](#stopplace-siteframe) — physical stop places defined in `stop.xml`.
2. [Quay (SiteFrame)](#quay-siteframe) — physical boarding locations (platforms) defined in `stop.xml`.
3. [PassengerStopAssignment (ServiceFrame)](#passengerstopassignment-serviceframe) — stable mapping from logical stops to StopPlace/Quay (defined in `line_<LineId>.xml`).
4. [ScheduledStopPoint (ServiceFrame)](#scheduledstoppoint-serviceframe) — logical stop points used in patterns and timetables (defined in `line_<LineId>.xml`).
5. [Parking (SiteFrame)](#parking-siteframe) — fixed parking places defined in `stop.xml` (including Bikebox).

---

## StopPlace (SiteFrame)

### Functional description

A **StopPlace** represents a physical location where passengers may access the public transport system (rail station, funicular station, shuttle stop).  
It serves as the **spatial anchor** for all stop-related elements referenced across the dataset.

A StopPlace is the authoritative source for:
- The official name of the station or stop,
- Its coordinates,
- Its internal components (Quays, Entrances, etc.),
- Its public-facing identifiers.

StopPlaces are defined **exclusively** in the *SiteFrame* and referenced from line files (ServiceFrame) through `PassengerStopAssignment` (see **PassengerStopAssignment (ServiceFrame)** below).  
They may also be referenced indirectly when journey-level quay/platform assignment is published (via `VehicleJourneyStopAssignment`, optional in MVP).

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|---------------------|
| `@id` | Identifier of the StopPlace | 1..1 | Must follow CFL identifier scheme (see 5.1). |
| `Name` | Public name of the stop or station | 1..1 | Name may contain one or more `<Text>` elements with `xml:lang`, allowing multilingual names. |
| `ShortName` | Optional short label | 0..1 | Used only if stable and meaningful. |
| `PublicCode` | Public-facing code | 0..1 | Unique if present (e.g. mnemonic code). |
| `Centroid/Location` | Geographic coordinates (WGS84) | 1..1 | Mandatory latitude/longitude. |
| `StopPlaceType` | Type of location | 1..1 | Typically `railStation`, `multimodalStopPlace`, etc. |
| `Quays` | List of quays belonging to the StopPlace | 0..n | At least one quay required. |
| `AccessibilityAssessment` | Accessibility summary | 0..1 | Not detailed in MVP (WP3 extension). |
| `TopographicPlaceRef` | Reference to locality | 0..1 | Optional, used if available. |
| `ParentSiteRef` | Hierarchical parent | 0..1 | Used for nested structures (optional). |

#### Notes
- Attributes not listed above are not used in the CFL MVP.
- Additional details (equipment, entrances, accessibility) may be added in later work packages.

---

### CFL-specific modelling rules

#### Names
- **Name** must reflect the official published station name.  
- Accents and hyphens are allowed.  
- If multilingual names are provided, they must be equivalent.

#### Identifiers
- Must follow the format:  
  **`LU:CFL:StopPlace:<LocalId>`**  
  e.g., `LU:CFL:StopPlace:SP00032`.

- Identifiers never change, even when the name is updated.

#### Coordinates
- Coordinates must be WGS84 decimal degrees.  
- Coordinates represent the *logical centre* of the station (not the platform).

#### Quays
- Every StopPlace must contain **at least one** Quay.  
- Quays must be defined within the same SiteFrame.  
- Quay identifiers must be globally unique.

---

### Minimal XML example (illustrative only)

⚠️ *This example is illustrative. Identifiers, names, coordinates and codes are not real CFL production data.*

```xml
<StopPlace id="LU:CFL:StopPlace:SP00032" version="1">
    <Name>Luxembourg Gare Centrale</Name>
    <Centroid>
        <Location>
            <Longitude>6.1333</Longitude>
            <Latitude>49.5995</Latitude>
        </Location>
    </Centroid>
    <StopPlaceType>railStation</StopPlaceType>

    <Quays>
        <QuayRef ref="LU:CFL:Quay:LuxGare-1" version="1"/>
        <QuayRef ref="LU:CFL:Quay:LuxGare-2" version="1"/>
    </Quays>
</StopPlace>
```

---

### Summary

The StopPlace is the foundational spatial object of the CFL timetable dataset.
It provides the authoritative definition of each station or stop, including:

- Stable identifier,
- Public name(s),
- Coordinates,
- Associated quays.

Other frames resolve StopPlaces through references, primarily via PassengerStopAssignment, ensuring consistency across the entire dataset.

---

## Quay (SiteFrame)

### Functional description

A **Quay** represents a physical boarding or alighting point within a StopPlace.  
For rail, this corresponds to a **platform** (e.g., “Platform 1”), a boarding edge, or a clearly identifiable passenger access point.

A Quay is:
- A **child element of a StopPlace**,
- A physical infrastructure object referenced when quay-level precision is required,
- Referenced via `QuayRef` in assignment structures (PassengerStopAssignment optional; VehicleJourneyStopAssignment optional)..

Each Quay belongs to **exactly one** StopPlace.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|-------------------------|----------------------|
| `@id` | Unique identifier of the Quay | 1..1 | Must follow CFL identifier scheme. |
| `Name` | Public platform/stop name | 1..1 | Multilingual `<Text>` elements allowed. |
| `PublicCode` | Public-facing platform code | 0..1 | If used, must be unique within the StopPlace. |
| `Description` | Additional textual info | 0..1 | Optional; avoid operational notes. |
| `Centroid/Location` | Coordinates of the quay | 0..1 | Optional in MVP; may be added in WP3. |
| `ParentSiteRef` | Reference to parent StopPlace | 1..1 | Must reference the enclosing StopPlace. |
| `QuayType` | Type of quay or platform | 0..1 | If used: `platform`, `bay`, etc. (NeTEx enumeration values). |

#### Notes
- In the MVP, equipment (signage, shelter, accessibility features) is **not** included.  
  These elements may be provided in WP3 “Accessibility”.
- A Quay must always be part of a StopPlace structure.

---

### CFL-specific modelling rules

#### Names
- **Name** must correspond to the public-facing platform or boarding label.  
  Examples: “Platform 1”, “Platform 3B”.  
- Multilingual representations may be provided via multiple `<Text>` elements.

#### Identifiers
- Must follow the format:  
  **`LU:CFL:Quay:<LocalId>`**  
  e.g., `LU:CFL:Quay:LuxGare-1`.

- Identifiers must be:
  - Globally unique,  
  - Stable across updates,  
  - Unchanged if the platform is renamed.

#### Relationship to StopPlace
- A Quay **must** have a `ParentSiteRef` pointing to its StopPlace.  
- Every StopPlace must contain one or more Quays.

---

### Minimal XML example (illustrative only)

⚠️ *Illustrative only — not real CFL data.*

```xml
<Quay id="LU:CFL:Quay:LuxGare-1" version="1">
    <Name>
        <Text xml:lang="fr">Quai 1</Text>
        <Text xml:lang="de">Bahnsteig 1</Text>
        <Text xml:lang="en">Platform 1</Text>
    </Name>
    <PublicCode>1</PublicCode>
    <ParentSiteRef ref="LU:CFL:StopPlace:SP00032"/>
</Quay>
```

### Usage in other NeTEx frames

#### Stable logical-to-physical mapping (ServiceFrame)

When a stable mapping to a physical location is published, a Quay may be referenced via:
- `PassengerStopAssignment/QuayRef` (optional)

Example:

```xml
<PassengerStopAssignment id="LU:CFL:PassengerStopAssignment:LuxGare" version="1">
    <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
    <StopPlaceRef ref="LU:CFL:StopPlace:SP00032"/>
    <QuayRef ref="LU:CFL:Quay:LuxGare-1"/>
</PassengerStopAssignment>

```

#### Journey-level quay/platform assignment (TimetableFrame, optional)

When quay/platform is known per journey, a Quay may be referenced via:
- `VehicleJourneyStopAssignment/QuayRef`

Example:
```xml
<VehicleJourneyStopAssignment id="LU:CFL:VehicleJourneyStopAssignment:LuxGare:VJ1234" version="1">
    <VehicleJourneyRef ref="LU:CFL:VehicleJourney:VJ1234"/>
    <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
    <QuayRef ref="LU:CFL:Quay:LuxGare-1"/>
</VehicleJourneyStopAssignment>
```

---

### Summary of CFL MVP restrictions

- Quays are mandatory within the StopPlace structure.
- No equipment or accessibility features in MVP.
- No detailed geometry required (option in WP3).
- Use of multilingual <Text> names encouraged when available.
- Stable and deterministic identifier scheme required.

---
## PassengerStopAssignment (ServiceFrame)

### Purpose and scope

A **PassengerStopAssignment** links a logical stop used in the service structure (`ScheduledStopPoint`) to the physical stop infrastructure (`StopPlace`, and optionally `Quay`).

It is the mechanism used in the CFL MVP to express the difference between:
- **logical stop points** (service design), and
- **physical stop places / boarding locations** (infrastructure).

PassengerStopAssignment is used to:
- map each logical stop (`ScheduledStopPoint`) to the physical station/stop area (`StopPlaceRef`);
- optionally specify a stable boarding location (`QuayRef`) when this assignment is considered structurally stable.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|---------------------|
| `@id` | Identifier of the PassengerStopAssignment | 1..1 | Must follow CFL identifier scheme. |
| `ScheduledStopPointRef` | Reference to the logical stop point | 1..1 | References a `ScheduledStopPoint` defined in the ServiceFrame. |
| `StopPlaceRef` | Reference to the physical StopPlace | 1..1 | References a `StopPlace` defined in the SiteFrame. |
| `QuayRef` | Reference to the physical Quay | 0..1 | Optional. Used only when a stable quay-level assignment is published. |

#### Notes
- `StopPlaceRef` is mandatory in the MVP.
- `QuayRef` is optional and should not be used for information that varies per journey; use `VehicleJourneyStopAssignment` for that case.

---

### CFL-specific modelling rules

- Each `ScheduledStopPoint` SHALL have exactly one `PassengerStopAssignment` linking it to a `StopPlaceRef`.
- `QuayRef` MAY be provided only when the quay/platform assignment is considered stable in published data.
- When quay assignment is known per journey (and may vary), it SHALL be modelled via `VehicleJourneyStopAssignment` (optional in MVP), not via `PassengerStopAssignment`.

---

### Minimal XML example (illustrative only)

```xml
<PassengerStopAssignment id="LU:CFL:PassengerStopAssignment:LuxGare" version="1">
    <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
    <StopPlaceRef ref="LU:CFL:StopPlace:SP00032"/>
</PassengerStopAssignment>
```

---
## ScheduledStopPoint (ServiceFrame)

### Purpose and scope

A **ScheduledStopPoint** represents a *logical stop* within a journey pattern, i.e.  a point where a vehicle may stop to allow passengers to board or alight.

It is:

- The **operational stop reference** used by JourneyPatterns and VehicleJourneys;
- The stop concept that appears in service design and timetable structures, independently from physical infrastructure details.

A ScheduledStopPoint is **not** a physical object.  

Its physical resolution is expressed through PassengerStopAssignment, which links it to a StopPlace (and optionally a stable Quay) defined in the SiteFrame.

In the CFL MVP:

- ScheduledStopPoints are used consistently in service patterns and timetable structures.
- The mapping to StopPlace/Quay is expressed via PassengerStopAssignment (and, when needed, via journey-level VehicleJourneyStopAssignment).

---

### Modelling principles

#### Logical vs Physical

A ScheduledStopPoint is *logical*:

- It identifies **where** a service stops in a commercial sequence (e.g., “Luxembourg”),
- Not **where exactly** a passenger boards (platform) or the physical geometry of the stop.

Physical details are handled via:
- `PassengerStopAssignment` (stable mapping to `StopPlace` / optional `Quay`), and
- `VehicleJourneyStopAssignment` when the quay/platform is known per journey (optional).

---

### Identifier strategy and rationale

In the CFL profile, `ScheduledStopPoint` identifiers are intentionally designed to be **human-readable** and derived from the public station name (e.g. “LuxGare”), rather than using opaque technical codes such as `SSP0001`.

This approach is chosen for several reasons:

#### Debuggability and maintainability
ScheduledStopPoints appear extensively in timetables (ServiceFrame patterns and TimetableFrame structures).  
Readable identifiers make it significantly easier for analysts, developers and operational staff to interpret timetable structures directly from XML files, logs, diffs or diagnostic tools, without requiring a lookup table.

#### Operational transparency
Many CFL teams (planning, SIV, operational support) consult or manipulate timetable data.  
Using meaningful identifiers allows non-technical users to immediately recognise stations when inspecting exports or troubleshooting integrations.

#### Stability and long-term maintainability
Unlike StopPlace identifiers, which may be aligned with a future national stop register, ScheduledStopPoints are internal timetable objects.  
Giving them human-readable identifiers ensures long-term clarity and avoids spreading opaque or temporary codes into downstream systems.

#### Alignment with other CFL identifiers
The CFL MVP already uses recognisable patterns for StopPlace and Quay identifiers.  
Using the same strategy for ScheduledStopPoint keeps the profile consistent and predictable across frames.

Therefore, the identifier pattern adopted in the CFL profile is:

`LU:CFL:ScheduledStopPoint:<ReadableStationId>`

where `<ReadableStationId>` is a short, human-friendly identifier derived from the public station name (e.g. “LuxGare”, “EschAlzette”, “Bettembourg”).

**Examples**
- `LU:CFL:ScheduledStopPoint:LuxGare`
- `LU:CFL:ScheduledStopPoint:EschAlzette`
- `LU:CFL:ScheduledStopPoint:Bettembourg`

**Important note:** Although ScheduledStopPoint identifiers are human-readable, data consumers should treat identifiers as opaque reference strings and should not derive semantics from them.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints | Example value |
|---------------------|-------------|------------------------|---------------------|--------------|
| `@id` | Unique identifier of the ScheduledStopPoint | 1..1 | Human-readable and derived from the public station label. | `LU:CFL:ScheduledStopPoint:LuxGare` |
| `version` | Object version | 1..1 | Incremented only when the semantic meaning of the stop changes. | `1` |
| `Name` | Human-readable stop name | 1..1 | Multilingual `<Text>` elements allowed. | `Luxembourg` |

#### Notes
- ScheduledStopPoint does not carry geographic coordinates; location is resolved via the mapped StopPlace/Quay.
- The mapping to physical infrastructure is expressed via PassengerStopAssignment.

---

### Rules and cardinalities

| Relationship / Rule | Cardinality | Description and LU-specific constraints |
|---------------------|-------------|------------------------------------------|
| ScheduledStopPoint → PassengerStopAssignment | 1..1 | Each ScheduledStopPoint SHALL be mapped to exactly one StopPlace via PassengerStopAssignment. |
| PassengerStopAssignment → StopPlaceRef | 1..1 | StopPlaceRef is mandatory in MVP. |
| PassengerStopAssignment → QuayRef | 0..1 | Optional. Used only when stable quay assignment is published. |
| VehicleJourneyStopAssignment → QuayRef | 0..1 | Optional. Used when quay/platform is known per journey. |
| StopPointInJourneyPattern → ScheduledStopPointRef | 1..1 | Each StopPointInJourneyPattern SHALL reference exactly one ScheduledStopPoint. |

#### Notes
- The ScheduledStopPoint defines the logical stop used in patterns and timetables.
- Ordering of stops is defined at the level of StopPointInJourneyPattern, never on the ScheduledStopPoint itself.

---

### XML example

The example below illustrates a typical CFL ScheduledStopPoint with a human-readable identifier.

```xml
<ScheduledStopPoint id="LU:CFL:ScheduledStopPoint:LuxGare" version="1">
    <Name>
        <Text xml:lang="fr">Luxembourg</Text>
        <Text xml:lang="de">Luxemburg</Text>
        <Text xml:lang="lb">Lëtzebuerg</Text>
    </Name>
</ScheduledStopPoint>
```
---

## Parking (SiteFrame)

### Functional description

A **Parking** represents a fixed parking place that can be used by passengers as part of their journey access/egress (e.g. car parks, bicycle parks, secured bicycle boxes).

In the CFL MVP, Parkings are published in the **SiteFrame** of `stop.xml`, because they are **place-based infrastructure objects** (not timetable objects).

In a first stage, Parkings are published as **standalone place objects** and can be consumed without relying on any link to `StopPlace`.

The target model may later introduce a differentiated approach:
- some Parkings will be **linked to a StopPlace** when the parking is part of a station site and the relationship is stable and maintainable;
- other Parkings will remain **unlinked** when no stable station attachment exists or when the parking is not functionally associated with a specific StopPlace.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|---------------------|
| `@id` | Identifier of the Parking | 1..1 | CFL identifier scheme (see 5.1). For Bikebox: includes the Bikebox code (e.g. `BK0001`). |
| `Name` | Public name/label of the parking | 1..1 | For Bikebox, includes the Bikebox code and station label. |
| `Description` | Passenger-facing description | 0..1 | For Bikebox, used to describe secure access conditions. |
| `Centroid/Location` | Geographic coordinates (WGS84) | 1..1 | Mandatory longitude/latitude. |
| `TypeOfParkingRef` | Parking type reference | 1..1 | MUST reference a `TypeOfParking` defined in `resource.xml`. |
| `TotalCapacity` | Total number of places | 0..1 | Mandatory for Bikebox in CFL MVP. |
| `ParkingPaymentProcess` | Payment model | 0..1 | Used for Bikebox (`free`). |
| `ParkingReservation` | Reservation / entitlement model | 0..1 | Used for Bikebox (`registrationRequired`). |
| `BookingUrl` | URL for booking/registration | 0..1 | Mandatory for Bikebox in CFL MVP. |
| `parkingProperties/ParkingProperties` | Additional parking properties | 0..n | Used for Bikebox (secure parking, max stay, allowed vehicle types). |
| `placeEquipments/*` | Equipment installed at the parking | 0..n | Optional in MVP; used when known (e.g. CCTV, access control). |

#### Notes
- Attributes not listed above are not used in the CFL MVP.
- `StopPlace` attachment is intentionally not required in the MVP (standalone publication), but may be introduced later for a subset of Parkings.

---

### CFL-specific modelling rules (generic)

- Parkings are published in `stop.xml` under `SiteFrame/.../parkings`.
- `TypeOfParkingRef` MUST point to a shared `TypeOfParking` value published in `resource.xml`.
- `Centroid/Location` MUST be provided.
- `TotalCapacity` SHOULD be provided whenever the capacity is known and stable.

---

### Minimal XML example (illustrative only)

⚠️ *Illustrative only — not real CFL data.*

```xml
<Parking id="LU:CFL:Parking:PARK001" version="1">
  <Name>Station X - Car park</Name>
  <Centroid>
    <Location>
      <Longitude>6.1000</Longitude>
      <Latitude>49.6000</Latitude>
    </Location>
  </Centroid>
  <TypeOfParkingRef ref="LU:CFL:TypeOfParking:CARPARK" versionRef="1"/>
  <TotalCapacity>120</TotalCapacity>
</Parking>
```

---

## bikebox (CFL specialisation of Parking)

### Purpose and scope

**bikebox** facilities are published as **secure bicycle parkings**. In the CFL MVP, they are modelled as `Parking` entities typed as **bikebox**, and are published as **standalone place objects** in `stop.xml`.

This section documents the CFL-specific rules for bikebox, on top of the generic `Parking` description above.

---

### Publication and frame

- Publication file: `stop.xml` *(renamed from the former `bikebox.xml`)*
- Frame: `SiteFrame` `CFL:SiteFrame:Bikebox:1`
- Content: `parkings/Parking` (one `Parking` per bikebox location)
- Prerequisite: `ResourceFrameRef ref="CFL:ResourceFrame:Resources:1" versionRef="1"`

---

### Typing and shared references

Each bikebox location MUST be represented by exactly one `Parking` using:

- `Parking@id` = `CFL:Parking:Bikebox:<BikeboxId>` (e.g. `CFL:Parking:Bikebox:BK0010`)
- `TypeOfParkingRef` = `CFL:TypeOfParking:BIKEBOX` (published in `resource.xml`)

The corresponding shared value is defined as:

- `TypeOfParking id="CFL:TypeOfParking:BIKEBOX" version="1"`

---

### Elements and constraints for bikebox (CFL MVP)

For bikebox Parkings, the following fields MUST be provided:

- `Centroid/Location` (longitude/latitude)
- `TypeOfParkingRef ref="CFL:TypeOfParking:BIKEBOX" versionRef="1"`
- `TotalCapacity`
- `ParkingPaymentProcess` (expected value: `free`)
- `ParkingReservation` (expected value: `registrationRequired`)
- `BookingUrl`
- `parkingProperties/ParkingProperties` including at least:
  - `ParkingVehicleTypes` (e.g. `cycle eCycle pedalCycle`)
  - `MaximumStay` (e.g. `P30D`)
  - `SecureParking` (`true`)

The following elements are OPTIONAL in the MVP and may be provided when known:

- `Description`
- `placeEquipments/*` (e.g. `PassengerSafetyEquipment/Cctv`, `VehicleReleaseEquipment/RemoteControl`)

---

### CFL-specific modelling rules

- bikebox are published as **standalone Parkings** in the MVP (no required link to `StopPlace`).
- If bikebox-to-station attachment is introduced later, it SHALL only be done when the relationship is stable and maintainable.
- bikebox identifiers are stable and MUST not change even if the public label is updated.

---

### XML example

```xml
<Parking id="CFL:Parking:Bikebox:BK0001" version="1">
  <Name>BK0001 - LUX01 - Sandweiler-Contern, Gare Nord</Name>
  <Description>Bikebox: secure bicycle parking. Access restricted to registered users. 24/7 access.</Description>
  <Centroid>
    <Location>
      <Longitude>6.13292</Longitude>
      <Latitude>49.598314</Latitude>
    </Location>
  </Centroid>
  <placeEquipments>
    <PassengerSafetyEquipment id="CFL:InstalledEquipment:Bikebox:BK0001:SAFETY" version="1">
      <Cctv>false</Cctv>
    </PassengerSafetyEquipment>
    <VehicleReleaseEquipment id="CFL:InstalledEquipment:Bikebox:BK0001:ACCESS_CONTROL" version="1">
      <RemoteControl>false</RemoteControl>
    </VehicleReleaseEquipment>
  </placeEquipments>
  <TypeOfParkingRef ref="CFL:TypeOfParking:BIKEBOX" versionRef="1"/>
  <TotalCapacity>32</TotalCapacity>
  <ParkingPaymentProcess>free</ParkingPaymentProcess>
  <ParkingReservation>registrationRequired</ParkingReservation>
  <BookingUrl>https://luxembourg.diwio.com/custom/luxembourg/register</BookingUrl>
  <parkingProperties>
    <ParkingProperties id="CFL:ParkingProperties:Bikebox:BK0001" version="1">
      <Name>Bikebox terms</Name>
      <ParkingVehicleTypes>cycle eCycle pedalCycle</ParkingVehicleTypes>
      <MaximumStay>P30D</MaximumStay>
      <SecureParking>true</SecureParking>
    </ParkingProperties>
  </parkingProperties>
</Parking>
```
