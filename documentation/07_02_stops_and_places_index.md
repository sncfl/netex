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
5. [Parking (SiteFrame)](#parking-siteframe) — fixed parking places defined in `parking.xml` (including Bikebox).
6. [Bikebox (CFL specialisation of Parking)](#bikebox-cfl-specialisation-of-parking) — secure bicycle parking facilities modelled as a CFL specialisation of `Parking`.

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

### Table of contents

- [Functional description and publication frame](#functional-description-and-publication-frame)
- [Scope of the CFL Parking model](#scope-of-the-cfl-parking-model)
- [Parking types and classification](#parking-types-and-classification)
- [Core modelling principles](#core-modelling-principles)
- [Recommended Parking structure](#recommended-parking-structure)
- [Capacity model](#capacity-model)
- [Physical structure and level modelling](#physical-structure-and-level-modelling)
- [Access, payment and reservation](#access-payment-and-reservation)
- [Counting, availability and smartparking](#counting-availability-and-smartparking)
- [Ownership and management](#ownership-and-management)
- [Elements and attributes retained in the CFL profile](#elements-and-attributes-retained-in-the-cfl-profile)
- [CFL-specific modelling rules](#cfl-specific-modelling-rules)
- [XML example](#xml-example-pr-parking-with-level-based-capacity-and-global-special-place-categories)

### Functional description and publication frame

A **Parking** represents a fixed parking facility that may be used by passengers as part of their journey access or egress.

In the CFL profile, Parkings are published in the **SiteFrame**, currently in `parking.xml`, because they are **place-based infrastructure objects** and not timetable objects.

A Parking may be modelled either as a standalone place object or, where relevant and maintainable, as a parking facility associated with a `StopPlace`.

In the CFL MVP, Parkings may be consumed as standalone objects. A stable relationship to a `StopPlace` may be introduced where the functional attachment to a station or stop is clear and maintainable.

---

### Scope of the CFL Parking model

The CFL Parking model covers physical parking facilities that are relevant for passenger access to or egress from the rail network.

In the CFL base profile, the following parking objects are included:

| Object | Included in the CFL Parking model | Modelling principle |
|--------|-----------------------------------|---------------------|
| P+R car parks | Yes | Modelled as `Parking` objects. |
| Bikebox facilities | Yes | Modelled as `Parking` objects, with a CFL-specific `TypeOfParkingRef` where needed. |
| Kiss & Rail | No, not as a physical parking object | Treated as a tariff rule, not as a `Parking`, `ParkingArea` or parking type. |
| Physical drop-off areas | No, not in the current CFL-managed scope | Excluded when managed by the city or by another external entity. |
| EWAP | No, not as physical capacity | Treated as an access right or internal quota, not as a `ParkingArea`, `ParkingBay` or physical category of places. |
| P+R Pass | No, not as physical capacity | Treated as an access right or internal quota, not as a `ParkingArea`, `ParkingBay` or physical category of places. |

Kiss & Rail is understood in the CFL business context as a tariff rule corresponding to a free short-stay period of 30 minutes in the CFL P+R tariff context.

It SHALL NOT be modelled as:

- a standalone `Parking`;
- a `ParkingArea`;
- a parking type;
- a parking place category;
- a reserved capacity;
- an access entitlement.

Physical drop-off areas may exist near stations. However, when these areas are managed by the city or by another external entity, they are outside the CFL-managed parking scope.

They SHALL therefore not be published as CFL-managed `Parking` objects unless a future scope extension explicitly requires externally managed drop-off facilities to be represented.

EWAP and P+R Pass are access rights or quota-based entitlements.

They SHALL NOT be modelled as:

- physical parking capacity;
- `ParkingArea`;
- `ParkingBay`;
- reserved spaces;
- special parking place categories.

Their detailed modelling belongs to the fare / access-right model or to internal access-control systems, not to the physical parking structure in the `SiteFrame`.

In the CFL P+R context, there is no guaranteed reservation of an individual parking space. Therefore, P+R parkings SHALL NOT be modelled as offering individual parking space reservation.

---

### Parking types and classification

The CFL Parking model distinguishes the functional type of a physical parking object.

The main parking types retained in the CFL base profile are:

| Parking type | Description | Main vehicle / user scope | Recommended representation |
|--------------|-------------|----------------------------|----------------------------|
| `P+R` | Park-and-ride car park associated with access to the rail network | Cars | `ParkingType = parkAndRide` |
| `Bikebox` | Secure bicycle parking facility | Bicycles / e-bikes / pedal cycles | `TypeOfParkingRef = LU:CFL:TypeOfParking:BIKEBOX` |

The parking type SHOULD first be expressed using standard NeTEx values where a suitable value exists.

For P+R parkings, the standard NeTEx value SHALL be used:

```xml
<ParkingType>parkAndRide</ParkingType>
```

For parking types that are not clearly covered by a standard NeTEx `ParkingType` value, a CFL-specific `TypeOfParkingRef` SHALL be used.

The proposed modelling rule is therefore:

| Parking type | Primary representation                            | Additional CFL classification                                                                                                         |
| ------------ | ------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `P+R` | `ParkingType = parkAndRide` | No CFL-specific `TypeOfParkingRef` required in the current profile. |
| `Bikebox`    | `TypeOfParkingRef = LU:CFL:TypeOfParking:BIKEBOX` | Used because no standard NeTEx `ParkingType` value clearly represents a secure bicycle box facility.                                  |

Kiss & Rail SHALL NOT be represented as a parking type because it is a tariff rule, not a physical parking object.

Physical drop-off areas are not included as CFL parking types in the current base profile when they are managed by the city or by another external entity.

The general principle is: **standard NeTEx classification first; CFL-specific reference values only where needed or explicitly useful for internal harmonisation**.

---

### Core modelling principles

The CFL Parking model is based on a clear separation between physical parking infrastructure, capacity information, access rights and tariff rules.

#### Parking

A `Parking` represents the physical parking facility as a whole.

It is the main object used to describe:

- the public name and location of the parking;
- the parking type;
- the global physical layout;
- the total physical capacity;
- the payment and reservation information;
- the availability of real-time occupancy data;
- the properties and capacities that apply to the parking as a whole.

#### ParkingArea

In the CFL base profile, `ParkingArea` is used only to represent a physical level or floor of a parking facility.

A `ParkingArea` SHALL be created only when level-based information is available and useful for publication or data consumption.

Typical level-based information includes:

- level name or number;
- level capacity;
- level-based availability;
- level-specific charging capacity;
- level-specific special place category capacity, where available.

`ParkingArea` SHALL NOT be used to represent:

- a tariff rule, such as Kiss & Rail or the first 30 minutes free;
- an access right, such as EWAP or P+R Pass;
- an internal quota;
- a non-physical entitlement;
- a management perimeter that is not also a physical level;
- a category of special places, such as PMR, charging, family, Flex, CFlex, Police or Customs places.

#### ParkingProperties and ParkingCapacity

Special parking place categories SHALL be represented as category-based capacity information, using the appropriate NeTEx capacity or property mechanism.

This applies to categories such as:

- PMR places;
- charging places;
- family places;
- Flex places, when physically identifiable;
- CFlex places, when physically identifiable;
- Police / Customs places, when physically identifiable.

When the exact physical level of a category is not known, the category-based capacity SHOULD be expressed at `Parking` level.

When the exact physical level of a category is known and intended for publication, the category-based capacity SHOULD be expressed within the relevant level-based `ParkingArea`.

A special parking place category SHALL NOT be modelled as a `ParkingArea` in the CFL base profile.

#### ParkingBay

The CFL base profile does not model individual parking spaces by default.

`ParkingBay` is therefore not retained in the CFL base profile.

It MAY be introduced in a later extension only if a specific use case requires individual parking space representation, for example a guaranteed reservation of an individual parking space.

This is not the case for EWAP or P+R Pass.

---

### Recommended Parking structure

The recommended CFL Parking structure is based on the following hierarchy:

```text
Parking
├── ParkingType [standard NeTEx type, when available]
├── TypeOfParkingRef [CFL-specific classification, when needed]
├── ParkingLayout
├── NumberOfParkingLevels
├── TotalCapacity
├── PrincipalCapacity
├── RechargingAvailable
├── RealTimeOccupancyAvailable
├── ParkingPaymentProcess [minimal payment indication only]
├── ParkingReservation [e.g. noReservations for P+R; registrationRequired where applicable]
├── BookingUrl [booking / registration URL; not for individual P+R space reservation]
├── parkingProperties
│   └── ParkingProperties
│       ├── ParkingVehicleTypes
│       ├── ParkingUserTypes
│       ├── ParkingStayList
│       ├── MaximumStay
│       ├── SecureParking
│       ├── ParkingVisibility
│       ├── MonitoredBays
│       └── spaces
│           └── ParkingCapacity [category-based capacity when not level-specific]
│               ├── ParkingUserType [e.g. registeredDisabled for PMR places]
│               ├── ParkingVehicleType
│               ├── ParkingStayType
│               ├── NumberOfSpaces
│               └── NumberOfSpacesWithRechargePoint
└── parkingAreas
    └── ParkingArea [physical level only]
        ├── TotalCapacity
        ├── NumberOfBaysWithRecharging
        ├── parkingProperties
        │   └── ParkingProperties
        │       └── spaces
        │           └── ParkingCapacity [category-based capacity when level-specific]
        ├── bays [not used by default]
        └── entrances
```

This structure supports both simple parking facilities and more detailed cases where the parking must be subdivided by physical level.

In the CFL base profile, the recommended granularity is:

```text
Parking
→ ParkingArea for physical levels, where level-based data is available
→ Category-based capacity within Parking, when not level-specific
→ Category-based capacity within ParkingArea, when level-specific
```

Individual parking spaces are not modelled by default. `ParkingBay` is kept outside the CFL base profile and may be introduced later only if a specific use case requires individual parking space representation, for example a real guaranteed reservation of an individual space. This is not the case for P+R Pass or EWAP.

---

### Capacity model

Capacity in the CFL Parking profile describes physical parking capacity.

It SHALL remain distinct from:

- tariff rules;
- access rights;
- internal quotas;
- entitlement-based access mechanisms;
- individual parking space reservations.

#### Total physical capacity

`Parking/TotalCapacity` describes the total physical capacity of the parking facility.

It SHOULD be provided whenever the total physical capacity is known and stable.

The total physical capacity includes all physical parking places that belong to the parking, including special place categories such as PMR, charging, family, Flex, CFlex, Police or Customs places.

These special categories are therefore included subsets of `Parking/TotalCapacity`. They SHALL NOT be added on top of the total capacity.

#### Level-based capacity

When capacity is known by physical level, one `ParkingArea` SHOULD be created per level.

`ParkingArea/TotalCapacity` describes the physical capacity of that level.

When all physical levels are represented, the sum of `ParkingArea/TotalCapacity` values SHOULD equal `Parking/TotalCapacity`.

In the CFL base profile, `ParkingArea` SHALL be used only for physical levels or floors.

It SHALL NOT be used for:

- management perimeters that are not also physical levels;
- tariff rules;
- access rights;
- internal quotas;
- special place categories.

#### Special place category capacity

Special place categories are represented as category-based capacity information, not as `ParkingArea`.

This applies to physical categories such as:

- PMR places;
- charging places;
- family places;
- Flex places, when physically identifiable;
- CFlex places, when physically identifiable;
- Police / Customs places, when physically identifiable.

Category-based capacities SHOULD be expressed using the appropriate NeTEx capacity or property mechanism, such as `ParkingProperties/spaces/ParkingCapacity`.

When the exact physical level of a category is not known, the category-based capacity SHOULD be expressed at `Parking` level.

When the exact physical level of a category is known and intended for publication, the category-based capacity SHOULD be expressed within the relevant level-based `ParkingArea`.

#### Access quotas and non-physical capacity

Physical parking capacity SHALL remain distinct from internal access quotas.

EWAP and P+R Pass allocated capacities are internal logical quotas.

They SHALL NOT be published as physical parking capacity and SHALL NOT be added to `Parking/TotalCapacity` or `ParkingArea/TotalCapacity`.

They SHALL NOT be represented as:

- `ParkingArea`;
- `ParkingBay`;
- reserved spaces;
- special parking place categories.

#### Individual parking spaces

The CFL base profile does not model individual parking spaces.

`ParkingBay` is therefore not used by default.

It MAY be introduced in a later extension only if a specific use case requires individual parking space representation, for example a guaranteed reservation of an individual parking space.

This is not the case for EWAP or P+R Pass.

#### Progressive data maturity and double-counting prevention

The CFL profile supports progressive data maturity for special parking place categories.

At an initial stage, special place category capacities may be known only globally for the whole parking.

At a later stage, the same category capacities may become available by physical level.

The model SHALL support both situations without requiring artificial `ParkingArea` elements for categories.

When only global category-based capacity is available, the category capacity SHOULD be expressed at `Parking` level.

Conceptual structure:

```text
Parking
├── TotalCapacity = 280
├── Category-based capacities
│   ├── PMR places = 8
│   ├── Charging places = 12
│   └── Family places = 6
└── parkingAreas
    ├── ParkingArea Level 0
    │   └── TotalCapacity = 140
    └── ParkingArea Level 1
        └── TotalCapacity = 140
```

In this case, PMR, charging and family capacities are known for the whole parking, but not by level.

They are included in `Parking/TotalCapacity`. They are not added on top of the level capacities.

When level-based category information is available and intended for publication, the category capacity SHOULD be expressed within the relevant level-based `ParkingArea`.

Conceptual structure:

```text
Parking
├── TotalCapacity = 280
└── parkingAreas
    ├── ParkingArea Level 0
    │   ├── TotalCapacity = 140
    │   └── Category-based capacities
    │       ├── PMR places = 4
    │       └── Charging places = 8
    └── ParkingArea Level 1
        ├── TotalCapacity = 140
        └── Category-based capacities
            ├── PMR places = 4
            ├── Charging places = 4
            └── Family places = 6
```

In this case, PMR, charging and family capacities are known by level.

They are included in the relevant `ParkingArea/TotalCapacity`. They are not added on top of the level capacities.

The same category SHOULD NOT be published at both `Parking` level and `ParkingArea` level, unless the `Parking`-level value is explicitly documented as an aggregate of the level-based values.

In all cases:

* the sum of level capacities SHOULD equal the total parking capacity, when all levels are represented;
* category-based capacities are included subsets of the relevant total capacity;
* category-based capacities SHALL NOT be added on top of `Parking/TotalCapacity` or `ParkingArea/TotalCapacity`;
* category-based capacities SHALL NOT be used to create parallel additive `ParkingArea` elements.

The refinement from parking-level category capacity to level-based category capacity SHALL preserve the same total-capacity interpretation and SHALL avoid double counting.

---

### Physical structure and level modelling

The CFL profile distinguishes between:

- the global physical layout of the parking facility;
- level-specific physical information, when the parking is modelled with level-based `ParkingArea` elements.

At `Parking` level, `ParkingLayout` SHOULD describe the overall layout of the parking.

When all levels have the same physical layout, the information MAY be provided only at `Parking` level.

When a parking contains both covered and uncovered levels, the parking SHOULD be considered mixed or partially covered at global level where a suitable `ParkingLayout` value is available.

The detailed covered / uncovered information by level is a CFL business requirement.

However, in the NeTEx XSD structures reviewed so far, `ParkingLayout` is available at `Parking` level. No dedicated standard element has been identified to encode covered / uncovered information directly at `ParkingArea` level.

Therefore, level-specific covered / uncovered information SHALL NOT be encoded using a non-standard element such as `ParkingArea/ParkingLayout` or `ParkingArea/Coverage`.

If this information must be published before a standard-compliant structure is confirmed, it MAY only be provided as descriptive information on the relevant `ParkingArea`, for example in a label or description field accepted by the implementation profile.

When a parking is multi-level, one `ParkingArea` SHOULD be created per physical level when level-based information is available.

Each level-based `ParkingArea` MAY then carry level-specific information such as:

- level name or number;
- level capacity;
- level-based availability;
- charging capacity;
- category-based capacities, such as PMR or family places, when known by level;
- descriptive indication of whether the level is covered or uncovered, if required and if encoded through an implementation-profile-compliant mechanism.

A global attribute at `Parking` level SHALL NOT be used to imply that all levels have the same characteristic when relevant level-specific differences are known and intended for publication.

This applies in particular to covered / uncovered information: when a parking contains both covered and uncovered levels, the global `Parking/ParkingLayout` SHALL only describe the overall mixed nature of the parking and SHALL NOT be interpreted as providing the covered / uncovered status of each individual level.

If level-specific information is required for publication, it must be provided separately for each level-based `ParkingArea`, using only a standard-compliant or implementation-profile-compliant mechanism.

Conversely, when all levels have the same physical layout, the information MAY be provided only at `Parking` level to avoid unnecessary duplication.

#### Level access and internal equipment

A parking may include internal equipment or infrastructure elements, such as:

- lifts;
- stairs;
- ramps;
- entrances;
- internal pedestrian paths;
- equipment relevant for accessibility or internal navigation.

These elements may be relevant for passenger accessibility, internal navigation and access to parking levels.

In the CFL MVP, such elements are optional and SHOULD be provided only when the information is available, reliable and useful for publication.

Equipment and internal access elements SHALL NOT be used to create artificial `ParkingArea` subdivisions.

When equipment is specific to a physical level, it SHOULD be associated with the relevant level-based `ParkingArea` where the NeTEx structure and the CFL implementation profile allow it.

---

### Access, payment and reservation

A `Parking` MAY include standard NeTEx information about payment and reservation facilities.

In the CFL P+R context, the payment model is one of the following:

| Business case | Interpretation | Recommended NeTEx representation |
|--------------|----------------|----------------------------------|
| Free parking | No payment is required | `ParkingPaymentProcess = free` |
| Payment via the P+R application | Payment and access are handled through the P+R app. This implies controlled access through a barrier and licence plate recognition. | `ParkingPaymentProcess = payByMobileDevice payByPlate`, and `PaymentByMobile` where app/payment URL information is available |
| Payment at the payment terminal | Used for users who have not installed the P+R app. The hourly tariff applies. | `ParkingPaymentProcess` should indicate a payment-required process; the detailed hourly tariff is handled in the fare model |

In the CFL profile, payment via the P+R application is understood as a combined business process covering:

- app-based payment;
- controlled access through a barrier;
- licence plate recognition.

These elements SHALL NOT be modelled as:

- parking types;
- `ParkingArea` elements;
- physical capacity;
- special parking place categories.

The physical access-control equipment MAY be documented where the appropriate NeTEx equipment structure is confirmed, but the main information retained in the parking profile is the payment and access process.

For CFL P+R parkings, `ParkingReservation` SHALL be set to `noReservations` when the information is published, because there is no guaranteed reservation of an individual parking space.

EWAP and P+R Pass SHALL NOT be represented as:

- `ParkingArea`;
- `ParkingBay`;
- reserved spaces;
- physical capacity;
- special parking place categories.

They are access rights or quota-based entitlements.

Their detailed modelling belongs to the fare / access-right model or to internal access-control systems, not to the physical parking structure in the `SiteFrame`.

Internal quotas associated with EWAP or P+R Pass SHALL NOT be published as customer-facing physical parking capacity.

The detailed hourly tariff, the conditional free-parking rule and the geographic eligibility zone SHALL be handled in the future NeTEx fare model, not in the physical parking structure.

`BookingUrl` SHALL be used only when a reliable booking or registration URL exists.

It SHALL NOT be used to imply that P+R spaces can be reserved individually.

For example, `BookingUrl` may be relevant for Bikebox registration where applicable, but not for individual P+R parking space reservation.

---

### Counting, availability and smartparking

Counting and availability information describe the operational monitoring of a parking facility.

They SHALL remain distinct from:

- the physical parking type;
- the physical capacity model;
- tariff rules;
- access rights;
- internal quotas.

The term **smartparking** is treated as a technical or operational qualification, not as a functional parking type.

A parking may be considered a smartparking when it is equipped with intelligent counting, monitoring or availability equipment.

Smartparking SHALL NOT be represented as:

- a `ParkingType`;
- a `TypeOfParkingRef`;
- a `ParkingArea`;
- a parking place category.

In NeTEx, the presence of real-time occupancy data SHOULD be represented using `RealTimeOccupancyAvailable` when applicable.

`RealTimeOccupancyAvailable` indicates whether dynamic occupancy information is available for the parking. It does not carry the dynamic occupancy value itself.

Dynamic parking status and occupancy information, where published, are provided through SIRI-FM. Static parking characteristics such as `TotalCapacity` remain published in NeTEx.

Availability information may be available:

- globally for the whole `Parking`;
- by physical level, when level-based `ParkingArea` elements are represented;
- for specific monitored bays, only if such information exists and is intended for publication.

The following counting configurations are identified in the CFL context:

| Counting configuration | Description | Modelling note |
|------------------------|-------------|----------------|
| No counting equipment | No automated counting or availability equipment is available. | `RealTimeOccupancyAvailable` should not be set to `true`. |
| Camera-based counting with parking map | Cameras are associated with a map of the parking places. | May support occupancy or bay-level monitoring, depending on the source data. |
| Dynamic entry / exit counting | Counting is performed at entry and exit barriers, with increment / decrement logic. | May support global or level-based availability. It may also support statistical information such as vehicle country of origin. |

Some parkings may also have a local display showing the number of remaining available places.

Where known, the model SHOULD document:

- whether real-time or near-real-time availability data exists;
- whether availability is available globally or by physical level;
- the counting method;
- whether level-based availability is directly measured or calculated;
- whether a local remaining-places display exists;
- the approximate update frequency, where available.

In the CFL P+R context, dynamic availability may be available by floor.

When availability is available by floor, it SHOULD be associated with the relevant level-based `ParkingArea`, where the NeTEx structure and CFL implementation profile allow it.

In some CFL P+R configurations, dynamic availability may be calculated using differential counting.

Where this applies:

- availability is measured per level, except for level 0;
- level 0 availability may be calculated rather than directly measured;
- counting errors may therefore be reflected in the calculated availability of level 0.

This distinction SHOULD be documented when level-based dynamic availability is published.

If individual bays are monitored, `MonitoredBays` may be used in `ParkingProperties`.

However, individual `ParkingBay` objects are not modelled by default in the CFL base profile.

Availability values SHALL NOT be confused with physical capacity values:

- `TotalCapacity` describes the stable physical capacity;
- availability describes the current or dynamic number of available places;
- internal EWAP or P+R Pass quotas SHALL NOT be published as physical capacity or as customer-facing availability unless a separate access-right model explicitly supports this distinction.

---

### Ownership and management

Ownership and management are distinct concepts.

A P+R may be owned by different entities, including:

- CFL;
- Ponts et Chaussées;
- Fonds du Rail;
- a municipality;
- another public or private entity.

The manager may be CFL or another entity.

Ownership or management information SHALL NOT drive the creation of `ParkingArea` elements. A `ParkingArea` may only be created when it represents a physical level or floor.

However, ownership or management information SHALL NOT drive the creation of `ParkingArea` elements.

In the CFL base profile, `ParkingArea` is reserved for physical levels or floors only.

A partially CFL-managed parking SHALL therefore NOT be subdivided into a `ParkingArea` only because part of the capacity is managed by CFL.

If a parking is partially managed by CFL, this information SHOULD be documented as business or operational metadata, where a suitable standard-compliant or implementation-profile-compliant mechanism exists.

Example: in Diekirch, CFL manages approximately 100 places through the P+R application out of a total of approximately 650 places.

This CFL-managed capacity is relevant for business understanding and access-control logic, but it SHALL NOT be represented as a `ParkingArea` unless it corresponds to a physical level or floor.

This allows the CFL profile to distinguish clearly between:

- the total physical capacity of the parking;
- the physical subdivision by level, where applicable;
- the capacity or quota managed through CFL operational processes;
- the access rules applicable to the CFL-managed part;
- the responsibility for management and data provision.

Managed capacity, access rights and internal quotas SHALL remain distinct from physical capacity and physical level modelling.

---

### Elements and attributes retained in the CFL profile

| Element / Attribute | Description | Cardinality (CFL profile) | Notes / Constraints |
|---------------------|-------------|----------------------------|---------------------|
| `@id` | Identifier of the `Parking` | 1..1 | Must follow the CFL identifier scheme. |
| `Name` | Public name or label of the parking | 1..1 | Should be understandable for passengers and operators. |
| `Description` | Passenger-facing or operational description | 0..1 | Optional. May be used to provide additional contextual information when no more specific standard-compliant structure is available. |
| `Centroid/Location` | Geographic coordinates, WGS84 | 1..1 | Mandatory longitude / latitude. |
| `ParkingType` | Standard NeTEx parking type | 0..1 | SHALL be used when a suitable standard value exists, e.g. `parkAndRide` for P+R. |
| `TypeOfParkingRef` | CFL-specific parking type reference | 0..1 | References a shared `TypeOfParking` in `resource.xml` when no suitable standard value exists or when CFL requires an internal functional classification, e.g. Bikebox. Kiss & Rail SHALL NOT be represented as a `TypeOfParkingRef`. |
| `ParkingLayout` | Global physical layout of the parking | 0..1 | Used at `Parking` level to describe the overall layout, such as open, covered, multi-storey or underground. It SHALL NOT be used to imply level-specific covered / uncovered information when the parking is mixed. |
| `NumberOfParkingLevels` | Number of physical levels | 0..1 | Should be provided for multi-level parkings and when known. |
| `TotalCapacity` | Total physical capacity of the parking | 0..1 | Should be provided when known and stable. Special place categories are included subsets of this capacity and SHALL NOT be added on top of it. |
| `PrincipalCapacity` | Main usable capacity | 0..1 | May be used when relevant and clearly distinguished from total physical capacity. |
| `parkingAreas/ParkingArea` | Physical level or floor of the parking | 0..n | Used only for physical levels or floors in the CFL base profile. SHALL NOT be used for tariff rules, access rights, internal quotas, management perimeters, non-physical entitlements or special place categories. |
| `ParkingArea/Name` | Name or label of the physical level | 0..1 | May be used to identify the level, e.g. Level 0, Level 1. |
| `ParkingArea/Description` | Descriptive information about the physical level | 0..1 | May be used for level-specific information such as covered / uncovered indication only when no dedicated standard-compliant structure is available and when accepted by the implementation profile. |
| `ParkingArea/TotalCapacity` | Physical capacity of the level | 0..1 | Used when capacity is known by level. When all levels are represented, the sum of level capacities SHOULD equal `Parking/TotalCapacity`. |
| `parkingProperties/ParkingProperties` | Additional parking properties | 0..n | Used for vehicle types, user types, secure parking, max stay, monitored bays and category-based capacity information. |
| `ParkingVehicleTypes` | Vehicle types allowed | 0..1 | Used to distinguish cars, cycles, e-bikes, etc. |
| `ParkingUserTypes` | User types associated with the parking or with a category-based capacity | 0..1 | May be used for special user categories, e.g. PMR / disabled users or families, where the relevant standard value is confirmed. |
| `spaces/ParkingCapacity` | Category-based capacity information | 0..n | Used to express special place category capacity, such as PMR, charging, family, Flex, CFlex, Police or Customs places. May be used at `Parking` level when the category is not level-specific, or within a level-based `ParkingArea` when the category is known by level. |
| `ParkingCapacity/ParkingUserType` | User category associated with a capacity | 0..1 | May be used for PMR or family capacity where the relevant standard value is confirmed. |
| `ParkingCapacity/ParkingVehicleType` | Vehicle category associated with a capacity | 0..1 | May be used where capacity is specific to a vehicle type. |
| `ParkingCapacity/ParkingStayType` | Stay category associated with a capacity | 0..1 | May be used only when a stay-related category is physically relevant and intended for publication. |
| `ParkingCapacity/NumberOfSpaces` | Number of spaces for a category | 0..1 | Category-based capacity is an included subset of the relevant total capacity and SHALL NOT be added on top of it. |
| `RechargingAvailable` | Indicates whether vehicle recharging is available | 0..1 | Relevant for charging facilities. |
| `NumberOfBaysWithRecharging` | Number of bays with charging facilities | 0..1 | May be used at `ParkingArea` level when charging capacity is known by level. |
| `ParkingCapacity/NumberOfSpacesWithRechargePoint` | Number of spaces with recharge point | 0..1 | May be used to express charging capacity as category-based capacity. |
| `ParkingStayList` | Type of parking stay | 0..1 | May be used only when the parking has an actual stay-related property to publish. SHALL NOT be used to model Kiss & Rail as a physical parking type. |
| `MaximumStay` | Maximum permitted stay | 0..1 | Used only when a maximum physical stay rule is directly applicable to the parking object. Detailed tariff rules, such as the first 30 minutes free, belong to the future fare model. |
| `SecureParking` | Indicates secure parking characteristics | 0..1 | Relevant especially for Bikebox or other secure parking facilities. |
| `ParkingVisibility` | Visibility or accessibility of the parking | 0..1 | Optional, when relevant and supported by the implementation profile. |
| `RealTimeOccupancyAvailable` | Indicates whether occupancy data is available | 0..1 | Used for smartparking / availability data. Does not represent capacity itself. |
| `MonitoredBays` | Indicates whether individual bays are monitored | 0..1 | Used only when such information exists. Individual `ParkingBay` objects are not modelled by default in the CFL base profile. |
| `ParkingPaymentProcess` | Payment model | 0..1 | Used when the payment model is known. In the CFL P+R context, the payment model is either free, payment via the P+R app, or payment at the payment terminal. |
| `PaymentByMobile` | Mobile payment information | 0..1 | May be used when payment through the P+R app is available and reliable app/payment URL information is known. |
| `ParkingReservation` | Reservation availability | 0..1 | For CFL P+R parkings, SHALL indicate that no reservation of an individual parking space is available when this information is published. SHALL NOT be used to model P+R Pass, EWAP or internal quotas. |
| `BookingUrl` | URL for booking or registration | 0..1 | Used only when a reliable booking or registration URL exists, e.g. for Bikebox registration where applicable. SHALL NOT be used to imply that P+R spaces can be reserved individually. |
| `placeEquipments` | Equipment container for the parking | 0..1 | Optional; used when equipment information is available and relevant. |
| `placeEquipments/PassengerSafetyEquipment` | Safety-related equipment information | 0..n | May be used for safety equipment, such as CCTV, where relevant. |
| `placeEquipments/VehicleReleaseEquipment` | Access-control-related equipment information | 0..n | May be used for access or release equipment where the appropriate structure is confirmed. |
| `ParkingBay` | Individual parking space | Not used by default | Not retained in the CFL base profile. May be introduced later only if a specific use case requires individual space representation, such as guaranteed reservation of an individual parking space. |

Note: in the CFL P+R context, payment via the P+R app is understood as a combined payment and access process. It implies controlled access through a barrier and licence plate recognition. These mechanisms are not modelled as parking types, `ParkingArea` elements, physical capacity or special parking place categories.

---

### CFL-specific modelling rules

- Parkings are published under `SiteFrame/.../parkings`, currently in `parking.xml`.
- `Parking` represents the physical parking facility as a whole.
- `Centroid/Location` MUST be provided.
- `TotalCapacity` SHOULD be provided whenever the physical capacity is known and stable.
- `ParkingType` SHALL be used when a suitable standard NeTEx value exists, e.g. `parkAndRide` for P+R.
- `TypeOfParkingRef` SHALL be used when no suitable standard `ParkingType` value exists or when a CFL-specific functional classification is explicitly required.
- When used, `TypeOfParkingRef` MUST point to a shared `TypeOfParking` value published in `resource.xml`.

- P+R and Bikebox are modelled as physical parking objects in the CFL profile.
- Kiss & Rail SHALL NOT be modelled as a parking type, `ParkingArea`, physical parking place category or reserved capacity.
- Kiss & Rail is a tariff rule corresponding to a free short-stay period of 30 minutes in the CFL P+R tariff context.
- Physical drop-off areas managed by the city or by another external entity are outside the CFL-managed parking scope unless a future scope extension explicitly includes them.

- In the CFL base profile, `ParkingArea` SHALL be used only for physical levels or floors.
- `ParkingArea` SHALL NOT be used for tariff rules, access rights, internal quotas, management perimeters, non-physical entitlements or special parking place categories.
- For multi-level parkings, one `ParkingArea` SHOULD be created per physical level when level-based information is available and useful for publication.
- `ParkingArea/TotalCapacity` SHOULD be provided when physical capacity is known by level.
- When all levels are represented, the sum of `ParkingArea/TotalCapacity` values SHOULD equal `Parking/TotalCapacity`.

- Special parking place categories SHALL be represented as category-based capacity information, not as `ParkingArea`.
- This applies to PMR, charging, family, Flex, CFlex, Police and Customs places, when such categories are known and intended for publication.
- When the exact physical level of a category is not known, category-based capacity SHOULD be expressed at `Parking` level.
- When the exact physical level of a category is known and intended for publication, category-based capacity SHOULD be expressed within the relevant level-based `ParkingArea`.
- Category-based capacities are included subsets of the relevant total capacity and SHALL NOT be added on top of `Parking/TotalCapacity` or `ParkingArea/TotalCapacity`.
- The same category SHOULD NOT be published at both `Parking` level and `ParkingArea` level unless the `Parking`-level value is explicitly documented as an aggregate of the level-based values.

- `ParkingLayout` SHOULD describe the global physical layout of the parking at `Parking` level.
- When a parking contains both covered and uncovered levels, `Parking/ParkingLayout` SHALL only describe the overall mixed nature of the parking.
- Level-specific covered / uncovered information SHALL NOT be encoded using non-standard elements such as `ParkingArea/ParkingLayout` or `ParkingArea/Coverage`.
- If level-specific covered / uncovered information must be published before a standard-compliant structure is confirmed, it MAY only be provided as descriptive information on the relevant `ParkingArea`, using a field accepted by the implementation profile.

- Smartparking is not a functional parking type; it is a technical or operational qualification derived from counting, monitoring or availability equipment.
- `RealTimeOccupancyAvailable` SHOULD be used when real-time or near-real-time occupancy data is available.
- When dynamic availability is available by physical level, it SHOULD be associated with the relevant level-based `ParkingArea`, where the NeTEx structure and CFL implementation profile allow it.
- In some CFL P+R configurations, availability is measured per level except for level 0, where it may be calculated. Counting errors may therefore be reflected in the calculated availability of level 0.

- In the CFL P+R context, the payment model is either free, payment via the P+R app, or payment at the payment terminal.
- Payment via the P+R app implies controlled access through a barrier and licence plate recognition.
- Payment at the payment terminal applies the hourly tariff for users who do not use the P+R app.
- The detailed hourly tariff, the conditional free-parking rule and the geographic eligibility zone SHALL be handled in the future NeTEx fare model, not in the physical parking structure.

- For CFL P+R parkings, `ParkingReservation` SHALL indicate that no reservation of an individual parking space is available when this information is published.
- `BookingUrl` SHALL NOT be used to imply that P+R spaces can be reserved individually.
- `BookingUrl` MAY be used only when a reliable booking or registration URL exists, e.g. for Bikebox registration where applicable.

- EWAP and P+R Pass are access rights or quota-based entitlements.
- EWAP and P+R Pass SHALL NOT be represented as physical capacity, `ParkingArea`, `ParkingBay`, reserved spaces or special parking place categories.
- Internal quotas associated with EWAP or P+R Pass SHALL NOT be published as customer-facing physical parking capacity.

- Individual parking spaces are not modelled by default.
- `ParkingBay` is not retained in the CFL base profile.
- `ParkingBay` MAY be introduced in a later extension only if a specific use case requires individual space representation, for example a guaranteed reservation of an individual parking space.
- This is not the case for EWAP or P+R Pass.

- Ownership and management information may be documented as business or operational metadata.
- Ownership or management information SHALL NOT drive the creation of `ParkingArea` elements unless the managed perimeter is also a physical level or floor represented in the profile.

---

### XML example: P+R parking with level-based capacity and global special place categories

⚠️ *Illustrative only — not real CFL production data.*

⚠️ *This example illustrates the CFL modelling principles. The exact XML structure for `ParkingProperties/spaces/ParkingCapacity`, the retained `ParkingPaymentProcess` values and the user-type values such as `registeredDisabled` or `families` must be validated against the CEN NeTEx XSD and the CFL implementation profile before being used as a production pattern.*

This example shows a multi-level P+R parking where:

- the parking is represented as one `Parking`;
- each physical level is represented as one `ParkingArea`;
- total capacity is known globally and by level;
- special place categories are known only globally, not yet by level;
- PMR, charging and family places are therefore represented at `Parking` level as category-based capacities;
- `ParkingArea` is not used for PMR, charging or family places;
- no individual `ParkingBay` is modelled;
- P+R spaces are not individually reservable.

```xml
<Parking id="LU:CFL:Parking:PR001" version="1">
  <Name>Station X - P+R</Name>
  <Description>Multi-level P+R parking with global special place category capacity and mixed covered / uncovered levels.</Description>

  <Centroid>
    <Location>
      <Longitude>6.1000</Longitude>
      <Latitude>49.6000</Latitude>
    </Location>
  </Centroid>

  <ParkingType>parkAndRide</ParkingType>
  <ParkingLayout>multistorey</ParkingLayout>
  <NumberOfParkingLevels>3</NumberOfParkingLevels>
  <TotalCapacity>420</TotalCapacity>
  <RechargingAvailable>true</RechargingAvailable>
  <RealTimeOccupancyAvailable>true</RealTimeOccupancyAvailable>
  <ParkingPaymentProcess>payByMobileDevice payByPlate</ParkingPaymentProcess>
  <ParkingReservation>noReservations</ParkingReservation>

  <parkingProperties>
    <ParkingProperties id="LU:CFL:ParkingProperties:PR001:Cars" version="1">
      <Name>Car parking properties</Name>
      <ParkingVehicleTypes>car</ParkingVehicleTypes>
    </ParkingProperties>

    <ParkingProperties id="LU:CFL:ParkingProperties:PR001:PMR" version="1">
      <Name>PMR places - whole parking</Name>
      <ParkingUserTypes>registeredDisabled</ParkingUserTypes>
      <spaces>
        <ParkingCapacity id="LU:CFL:ParkingCapacity:PR001:PMR" version="1">
          <ParkingUserType>registeredDisabled</ParkingUserType>
          <NumberOfSpaces>8</NumberOfSpaces>
        </ParkingCapacity>
      </spaces>
    </ParkingProperties>

    <ParkingProperties id="LU:CFL:ParkingProperties:PR001:Charging" version="1">
      <Name>Charging places - whole parking</Name>
      <spaces>
        <ParkingCapacity id="LU:CFL:ParkingCapacity:PR001:Charging" version="1">
          <NumberOfSpacesWithRechargePoint>12</NumberOfSpacesWithRechargePoint>
        </ParkingCapacity>
      </spaces>
    </ParkingProperties>

    <ParkingProperties id="LU:CFL:ParkingProperties:PR001:Family" version="1">
      <Name>Family places - whole parking</Name>
      <ParkingUserTypes>families</ParkingUserTypes>
      <spaces>
        <ParkingCapacity id="LU:CFL:ParkingCapacity:PR001:Family" version="1">
          <ParkingUserType>families</ParkingUserType>
          <NumberOfSpaces>6</NumberOfSpaces>
        </ParkingCapacity>
      </spaces>
    </ParkingProperties>
  </parkingProperties>

  <parkingAreas>
    <ParkingArea id="LU:CFL:ParkingArea:PR001:Level0" version="1">
      <Name>Station X - P+R - Level 0</Name>
      <Description>Covered level. Covered / uncovered information is provided descriptively until a standard-compliant level-specific structure is confirmed.</Description>
      <TotalCapacity>140</TotalCapacity>
    </ParkingArea>

    <ParkingArea id="LU:CFL:ParkingArea:PR001:Level1" version="1">
      <Name>Station X - P+R - Level 1</Name>
      <Description>Covered level. Covered / uncovered information is provided descriptively until a standard-compliant level-specific structure is confirmed.</Description>
      <TotalCapacity>140</TotalCapacity>
    </ParkingArea>

    <ParkingArea id="LU:CFL:ParkingArea:PR001:Level2" version="1">
      <Name>Station X - P+R - Level 2</Name>
      <Description>Uncovered level. Covered / uncovered information is provided descriptively until a standard-compliant level-specific structure is confirmed.</Description>
      <TotalCapacity>140</TotalCapacity>
    </ParkingArea>
  </parkingAreas>
</Parking>
```

In this example, the level capacities are additive:

```text
Parking/TotalCapacity = Level 0 + Level 1 + Level 2
420 = 140 + 140 + 140
```

The special place category capacities are not additive to the total capacity:

```text
PMR places = 8
Charging places = 12
Family places = 6
```

These are included subsets of `Parking/TotalCapacity`.

Because the category distribution by level is not yet available, these category-based capacities are published at `Parking` level only.

When the data becomes available by level, the same category-based capacities may be moved into the relevant level-based `ParkingArea`, without creating artificial `ParkingArea` elements for categories.

---

## Bikebox (CFL specialisation of Parking)

### Purpose and scope

**bikebox** facilities are published as **secure bicycle parkings**. In the CFL profile, each bikebox location is modelled as one `Parking` entity typed as `BIKEBOX`. Bikebox facilities are therefore not modelled as equipment of a station, but as dedicated `Parking` objects. This section documents the CFL-specific rules for bikebox parkings, on top of the generic `Parking` description above.

---

### Publication and frame

In the current CFL dataset, bikebox facilities are published in `parking.xml`. 

The current publication uses a dedicated `SiteFrame` for bikebox parkings: 
- Publication file: `parking.xml`
- Frame: `SiteFrame`
- Current frame identifier: `LU:CFL:SiteFrame:Bikebox:1`
- Content: `parkings/Parking`
- One `Parking` is published per bikebox location
- Prerequisite: `ResourceFrameRef ref="LU:CFL:ResourceFrame:Resources:1" versionRef="1"`

The current implementation therefore publishes bikebox facilities as standalone `Parking` objects. 

A future consolidation may decide whether bikebox parkings remain in a dedicated `parking.xml` file or are merged into a broader `stop.xml` / stops-and-places publication structure. 

--- 

### Typing and shared references

Each bikebox location SHALL be represented by exactly one `Parking` using: 
- `TypeOfParkingRef = LU:CFL:TypeOfParking:BIKEBOX`

The corresponding shared value SHALL be defined in `resource.xml` as a `TypeOfParking`. 

Current pattern:

```xml <TypeOfParkingRef ref="LU:CFL:TypeOfParking:BIKEBOX" versionRef="1"/> ```

No standard `ParkingType` value is currently required for bikebox, since the available standard NeTEx parking types do not clearly represent a secure bicycle box facility.

---

### Identifier strategy

Bikebox parking identifiers follow the current CFL pattern:

```text
LU:CFL:Parking:Bikebox:<BikeboxId>
```

Examples:

```text
LU:CFL:Parking:Bikebox:BK-1
LU:CFL:Parking:Bikebox:BK-2
LU:CFL:Parking:Bikebox:BK-3
```

The `BikeboxId` is the stable technical bikebox identifier used in the source data.

Bikebox identifiers SHALL remain stable over time and SHALL NOT change when the public label or station label is updated.

---

### Naming rule

The `Name` of a bikebox parking SHALL contain the bikebox location label provided by the authoritative source system.

Current examples:

```text
LUX01 - Sandweiler-Contern, Gare Nord
LUX03 - Troisvierges Gare
LUX04 - Clervaux Gare
```

The name does not need to repeat the technical `Parking@id`, but it SHOULD remain understandable for operational users and data consumers.

Multilingual names may be provided when available.

---

### Description

A bikebox parking MAY include a passenger-facing description.

Current standard description:

```text
Bikebox: secure bicycle parking. Access restricted to registered users. 24/7 access.
```

This description indicates that:

- the facility is a secure bicycle parking;
- access is restricted to registered users;
- access is available 24/7.

---

### Elements and constraints for Bikebox

For bikebox parkings, the following fields SHALL be provided:

| Element / Attribute | Cardinality | Rule / Expected value |
|---------------------|-------------|------------------------|
| `@id` | 1..1 | Stable identifier following the current CFL bikebox pattern, e.g. `LU:CFL:Parking:Bikebox:BK-1`. |
| `version` | 1..1 | Current value: `1`. |
|`Name/Text` | 1..1 | Bikebox location label provided by the authoritative source system, e.g. `LUX01 - Sandweiler-Contern, Gare Nord`. |
| `Description/Text` | 1..1 | Current standard description of secure access conditions. |
| `Centroid/Location/Longitude` | 1..1 | Mandatory WGS84 longitude. |
| `Centroid/Location/Latitude` | 1..1 | Mandatory WGS84 latitude. |
| `placeEquipments/PassengerSafetyEquipment` | 1..1 | Safety equipment container currently provided for each bikebox. |
| `placeEquipments/PassengerSafetyEquipment/Cctv` | 1..1 | Current value: `false`. |
| `placeEquipments/VehicleReleaseEquipment` | 1..1 | Access-control equipment container currently provided for each bikebox. |
| `placeEquipments/VehicleReleaseEquipment/RemoteControl` | 1..1 | Current value: `false`. |
| `TypeOfParkingRef` | 1..1 | Must reference `LU:CFL:TypeOfParking:BIKEBOX` with `versionRef="1"`. |
| `TotalCapacity` | 1..1 | Total number of bicycle parking spaces for the Bikebox. The value is provided individually for each Bikebox. |
| `RealTimeOccupancyAvailable` | 1..1 | Indicates whether the Bikebox can provide dynamic occupancy information. `true` when a real-time occupancy source is available, otherwise `false`. |
| `ParkingPaymentProcess` | 1..1 | Current value: `free`. |
| `ParkingReservation` | 1..1 | Current value: `registrationRequired`. |
| `parkingProperties/ParkingProperties` | 1..1 | One `ParkingProperties` element currently provided per bikebox. |
| `parkingProperties/ParkingProperties/Name/Text` | 1..1 | Current value: `Bikebox terms`. |
| `parkingProperties/ParkingProperties/ParkingVehicleTypes` | 1..1 | Current value: `cycle eCycle pedalCycle`. |
| `parkingProperties/ParkingProperties/MaximumStay` | 1..1 | Current value: `P30D`. |
| `parkingProperties/ParkingProperties/SecureParking` | 1..1 | Current value: `true`. |

---

### Equipment

Bikebox facilities may include equipment information in `placeEquipments`.

The current implementation uses:

- `PassengerSafetyEquipment` for safety-related information, such as CCTV;
- `VehicleReleaseEquipment` for access-control-related information, such as remote control.

Current pattern:

```xml
<placeEquipments>
  <PassengerSafetyEquipment id="LU:CFL:InstalledEquipment:Bikebox:BK-1:SAFETY" version="1">
    <Cctv>false</Cctv>
  </PassengerSafetyEquipment>
  <VehicleReleaseEquipment id="LU:CFL:InstalledEquipment:Bikebox:BK-1:ACCESS_CONTROL" version="1">
    <RemoteControl>false</RemoteControl>
  </VehicleReleaseEquipment>
</placeEquipments>
```

When provided, equipment identifiers SHOULD follow a stable and deterministic pattern based on the bikebox identifier.

---

### Parking properties

Each bikebox parking SHALL include `ParkingProperties`.

The current implementation uses one `ParkingProperties` element per bikebox parking.

Current pattern:

```xml
<parkingProperties>
  <ParkingProperties id="LU:CFL:ParkingProperties:Bikebox:BK-1" version="1">
    <Name>
      <Text lang="en">Bikebox terms</Text>
    </Name>
    <ParkingVehicleTypes>cycle eCycle pedalCycle</ParkingVehicleTypes>
    <MaximumStay>P30D</MaximumStay>
    <SecureParking>true</SecureParking>
  </ParkingProperties>
</parkingProperties>
```

The following rules apply:

- `ParkingVehicleTypes` SHALL indicate that bicycles and relevant bicycle variants are allowed.
- `MaximumStay` SHALL be provided when the maximum authorised stay is known.
- `SecureParking` SHALL be set to `true`.

---

### Booking and registration

Bikebox access is restricted to registered users.

This is currently represented through:

```xml
<ParkingReservation>registrationRequired</ParkingReservation>
```

A `BookingUrl` may be provided when an authoritative registration or booking URL is available and stable.

Unlike earlier drafts of this profile, `BookingUrl` is not considered mandatory for bikebox unless the source data reliably provides it.

---

### CFL-specific modelling rules for Bikebox

- Bikebox facilities are modelled as `Parking` entities.
- Each bikebox location SHALL be represented by one `Parking`.
- Bikebox parkings SHALL use `TypeOfParkingRef = LU:CFL:TypeOfParking:BIKEBOX`.
- Bikebox parkings are currently published in `parking.xml` within `SiteFrame id="LU:CFL:SiteFrame:Bikebox:1"`.
- Bikebox parkings are currently published as standalone place objects.
- A link to `StopPlace` is not mandatory in the current bikebox profile.
- `TotalCapacity` SHALL be provided.
- `ParkingPaymentProcess` SHALL be `free`.
- `ParkingReservation` SHALL be `registrationRequired`.
- `ParkingVehicleTypes` SHALL include bicycle-related vehicle types.
- `SecureParking` SHALL be `true`.
- `MaximumStay` SHALL be provided when the rule is known.
- `BookingUrl` MAY be provided, but is not mandatory unless reliable source data is available.
- Equipment information MAY be provided through `placeEquipments`.

* Static Bikebox characteristics, including `TotalCapacity`, are published in NeTEx.
* Dynamic operational status and occupancy information, when available, are published through SIRI-FM.
* The corresponding SIRI-FM `FacilityRef` SHALL use the identifier of the NeTEx `Parking` object.
* `RealTimeOccupancyAvailable` SHALL be provided for each Bikebox.
* `RealTimeOccupancyAvailable = true` indicates that dynamic occupancy information can be provided for the Bikebox.
* `RealTimeOccupancyAvailable = false` indicates that no dynamic occupancy information is available for the Bikebox.
* The value indicates the availability of occupancy monitoring only; the actual dynamic occupancy value is published through SIRI-FM.
---

### XML example

⚠️ *Illustrative example based on the current CFL bikebox structure.*

```xml
<Parking id="LU:CFL:Parking:Bikebox:BK-1" version="1">
  <Name>
    <Text lang="en">LUX01 - Sandweiler-Contern, Gare Nord</Text>
  </Name>
  <Description>
    <Text lang="en">Bikebox: secure bicycle parking. Access restricted to registered users. 24/7 access.</Text>
  </Description>
  <Centroid>
    <Location>
      <Longitude>6.21269002</Longitude>
      <Latitude>49.59911599</Latitude>
    </Location>
  </Centroid>
  <placeEquipments>
    <PassengerSafetyEquipment id="LU:CFL:InstalledEquipment:Bikebox:BK-1:SAFETY" version="1">
      <Cctv>false</Cctv>
    </PassengerSafetyEquipment>
    <VehicleReleaseEquipment id="LU:CFL:InstalledEquipment:Bikebox:BK-1:ACCESS_CONTROL" version="1">
      <RemoteControl>false</RemoteControl>
    </VehicleReleaseEquipment>
  </placeEquipments>
  <TypeOfParkingRef ref="LU:CFL:TypeOfParking:BIKEBOX" versionRef="1"/>
  <TotalCapacity>32</TotalCapacity>
  <RealTimeOccupancyAvailable>true</RealTimeOccupancyAvailable>
  <ParkingPaymentProcess>free</ParkingPaymentProcess>
  <ParkingReservation>registrationRequired</ParkingReservation>
  <parkingProperties>
    <ParkingProperties id="LU:CFL:ParkingProperties:Bikebox:BK-1" version="1">
      <Name>
        <Text lang="en">Bikebox terms</Text>
      </Name>
      <ParkingVehicleTypes>cycle eCycle pedalCycle</ParkingVehicleTypes>
      <MaximumStay>P30D</MaximumStay>
      <SecureParking>true</SecureParking>
    </ParkingProperties>
  </parkingProperties>
</Parking>
```

---



