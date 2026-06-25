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

### Functional description

A **Parking** represents a fixed parking facility that may be used by passengers as part of their journey access or egress.

In the CFL profile, Parkings are published in the **SiteFrame**, currently in `parking.xml`, because they are **place-based infrastructure objects** and not timetable objects.

The CFL Parking model covers, at least:

- P+R car parks;
- Bikebox facilities, considered as a specific type of secure bicycle parking.

Kiss & Rail is not modelled as a physical parking type in the CFL profile. It is understood as a tariff rule corresponding to a free short-stay period of 30 minutes in the CFL P+R tariff context.

Physical drop-off areas may exist near stations, but they are not managed by CFL in the current business scope. They are therefore not included as CFL-managed `Parking` objects unless a future scope extension explicitly requires external or city-managed drop-off facilities to be represented.

A Parking may be modelled either as a standalone place object or, where relevant and maintainable, as a parking facility associated with a `StopPlace`.

In the CFL MVP, Parkings may be consumed as standalone objects. A stable relationship to a `StopPlace` may be introduced where the functional attachment to a station or stop is clear and maintainable.

---

### Parking types in the CFL profile

The main functional parking types identified in the CFL context are:

| Parking type | Description | Main vehicle / user scope | Notes |
|--------------|-------------|----------------------------|-------|
| `P+R` | Park-and-ride parking associated with access to the rail network | Cars | Motorcycles are not allowed in CFL P+R parkings. |
| `Bikebox` | Secure bicycle parking facility | Bicycles / e-bikes / pedal cycles | Modelled as a specific type of `Parking`. |

Kiss & Rail is excluded from the parking type list because it is a tariff rule, not a physical parking type.

The parking type SHOULD first be expressed using standard NeTEx values where a suitable value exists.

For P+R parkings, the standard NeTEx value SHALL be used:

```xml
<ParkingType>parkAndRide</ParkingType>
```

For parking types that are not clearly covered by a standard NeTEx `ParkingType` value, a CFL-specific `TypeOfParkingRef` SHALL be used.

The proposed modelling rule is therefore:

| Parking type | Primary representation | Additional CFL classification |
|--------------|------------------------|-------------------------------|
| `P+R` | `ParkingType = parkAndRide` | `TypeOfParkingRef = LU:CFL:TypeOfParking:P_R` only if CFL decides to keep a harmonised internal classification for all parking types. |
| `Bikebox` | `TypeOfParkingRef = LU:CFL:TypeOfParking:BIKEBOX` | Used because no standard NeTEx `ParkingType` value clearly represents a secure bicycle box facility. |

Kiss & Rail SHALL NOT be represented as a parking type. It is a tariff rule corresponding to a free short-stay period of 30 minutes in the CFL P+R tariff context.

Physical drop-off areas SHALL NOT be represented as CFL-managed `Parking` objects unless they are explicitly brought into the CFL publication scope.

The general principle is: **standard NeTEx classification first; CFL-specific reference values only where needed or explicitly useful for internal harmonisation**.

---

### Parking structure and subdivision

A `Parking` may be subdivided when the subdivision represents a meaningful physical or management perimeter.

In NeTEx, this subdivision is represented using `ParkingArea`.

In the CFL profile, `ParkingArea` SHALL NOT be used as a generic container for any business distinction. It SHALL primarily represent physical or clearly bounded subdivisions of a parking facility.

The preferred use of `ParkingArea` in the CFL profile is:

- one `ParkingArea` per physical level or floor when the parking is multi-level and capacity or availability is known by level;
- one `ParkingArea` for a clearly identified CFL-managed part of a larger parking, when the CFL-managed perimeter is distinct and the data allows it.

`ParkingArea` SHALL NOT be used to represent:

- a tariff rule, such as Kiss & Rail or the first 30 minutes free;
- an access right, such as EWAP or P+R Pass;
- an internal quota;
- a non-physical entitlement;
- a category of places if the category is only an included subset of a level or of the total parking capacity.

Parking place categories such as PMR, Chargy, Family, Flex, CFlex, Police or Customs SHOULD be represented as capacity details or properties within the relevant `Parking` or `ParkingArea`, not as parallel additive `ParkingArea` elements, unless they correspond to clearly distinct and non-overlapping physical areas.

The CFL profile does not model individual parking spaces by default.

Capacity is modelled at `Parking` level or, when more detailed data is available, at `ParkingArea` level. Category-based capacity may then be expressed as additional detail within the relevant level or managed area.

If individual parking spaces need to be represented in a later extension, `ParkingBay` may be used. It is not retained by default in the CFL base profile.

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
├── ParkingReservation [only for real booking / registration cases if applicable, not for P+R Pass or EWAP]
├── BookingUrl [only when a reliable booking or registration URL exists]
├── parkingProperties
│   └── ParkingProperties
│       ├── ParkingVehicleTypes
│       ├── ParkingUserTypes
│       ├── ParkingStayList
│       ├── MaximumStay
│       ├── SecureParking
│       ├── ParkingVisibility
│       └── MonitoredBays
└── parkingAreas
    └── ParkingArea
        ├── TotalCapacity
        ├── NumberOfBaysWithRecharging
        ├── parkingProperties
        ├── bays [not used by default]
        └── entrances
```

This structure supports both simple parking facilities and more detailed cases where the parking must be subdivided by physical level or by a clearly identified management perimeter.

In the CFL base profile, the recommended granularity is:

```text
Parking
→ ParkingArea for physical levels or clearly bounded managed areas, where needed
→ Capacity details by category within the relevant Parking or ParkingArea, where available
```

Individual parking spaces are not modelled by default. `ParkingBay` is kept outside the CFL base profile and may be introduced later only if a specific use case requires individual parking space representation, for example a real guaranteed reservation of an individual space. This is not the case for P+R Pass or EWAP.

---

### ParkingArea and capacity modelling rules

`ParkingArea` is used to describe meaningful physical or management subdivisions of a `Parking`.

A `ParkingArea` SHALL be created only when the subdivision carries useful information for publication or data consumption and corresponds to a clearly identifiable perimeter.

In the CFL base profile, the preferred use of `ParkingArea` is level-based subdivision.

Typical cases include:

| Business case | Recommended use of `ParkingArea` |
|---------------|----------------------------------|
| Parking level or floor | Use one `ParkingArea` per physical level when capacity or availability is known by level. |
| CFL-managed part of a larger parking | Use a dedicated `ParkingArea` only when the CFL-managed part is clearly identifiable as a distinct management perimeter. |
| Specific category of parking places | Do not create a parallel `ParkingArea` by default. Represent the category as capacity or property detail within the relevant `Parking` or level `ParkingArea`, unless the category corresponds to a distinct, non-overlapping physical area. |
| Specific access right, pass or entitlement | Do not use `ParkingArea`. EWAP and P+R Pass are rights or quotas, not physical areas. |
| Specific tariff rule | Do not use `ParkingArea`. Kiss & Rail, free short stay and hourly tariff rules are tariff rules, not physical areas. |
| Specific counting or availability coverage | Use `ParkingArea` only if the counting perimeter corresponds to a physical level or clearly bounded area. |

A `ParkingArea` SHOULD NOT be created only to duplicate information already expressed at `Parking` level.

A `ParkingArea` SHALL NOT be created for internal quotas that are not intended for publication.

---

#### Capacity modelling

Capacity MAY be provided at different levels of detail, depending on the available source data.

The preferred order is:

1. `Parking/TotalCapacity` for the total physical parking capacity;
2. `ParkingArea/TotalCapacity` when the capacity is known for a physical level or for a clearly identified managed part;
3. `ParkingCapacity/NumberOfSpaces` when capacity must be expressed by user type, vehicle type or stay type within the relevant `Parking` or `ParkingArea`;
4. `NumberOfBaysWithRecharging` or `NumberOfSpacesWithRechargePoint` for charging places.

When capacity is known by floor, one `ParkingArea` SHOULD be created per physical level.

When dynamic availability is available by floor, it SHOULD be associated with the relevant level-based `ParkingArea`, where the data model and publication format allow it.

In the CFL P+R context, dynamic availability may be calculated using differential counting.

Where this applies:

- availability is measured per level, except for level 0;
- level 0 availability may be calculated rather than directly measured;
- counting errors may therefore be reflected in the calculated availability of level 0.

This distinction SHOULD be documented when level-based dynamic availability is published.

Physical parking capacity SHALL remain distinct from internal access quotas.

EWAP and P+R Pass allocated capacities are internal logical quotas. They SHALL NOT be published as physical parking capacity and SHALL NOT be added to `Parking/TotalCapacity` or `ParkingArea/TotalCapacity`.

The CFL base profile does not model individual parking spaces.

`ParkingBay` is therefore not used by default. It MAY be introduced in a later extension only if a specific use case requires individual parking space representation, for example a guaranteed reservation of an individual parking space. This is not the case for EWAP or P+R Pass.

---

#### Combining level-based and category-based capacity

When both level-based and category-based capacity information is available, the physical level SHOULD be used as the primary `ParkingArea` subdivision.

Category-based capacities SHOULD then be expressed within the relevant level area, using the appropriate NeTEx capacity or property mechanism.

In the current CFL business context, special parking place categories such as PMR or electric charging places are not necessarily communicated to customers by level.

The CFL profile nevertheless allows category-based capacities to be expressed within the relevant level `ParkingArea` when the data becomes available and when publication by level is required.

Until such level-based category information is available and intended for publication, category-based capacities MAY remain expressed at `Parking` level or as non-level-specific capacity details.

This means that:

- the sum of level capacities SHOULD equal the total parking capacity;
- category-based capacities are treated as included subsets of the relevant level capacity;
- category-based capacities SHALL NOT be added on top of level capacities unless the data explicitly confirms that they represent separate, non-overlapping areas.

Conceptual structure when level-based category information is available and intended for publication:

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
            └── Family places = 6
```

In this example, PMR, charging and family places are included in the level capacities. They are not added on top of the level capacities.

The profile SHALL avoid modelling level areas and category areas as parallel additive `ParkingArea` elements unless the data explicitly confirms that they are non-overlapping.

---

### Physical structure

A Parking SHALL NOT be described only through a generic “surface parking” / “building parking” distinction.

The CFL profile distinguishes at least two independent physical characteristics:

| Characteristic | Description | Recommended NeTEx representation |
|----------------|-------------|----------------------------------|
| Number of levels | Indicates whether the parking has one or several levels | `NumberOfParkingLevels` |
| Coverage / layout | Indicates whether the parking is covered, open, multi-storey, underground, etc. | `ParkingLayout` |

This distinction is required because a parking may be on a single level and still be covered, for example by a roof structure or solar panels.

If only minimal information is available, the following simplified attributes may be derived:

- single-level / multi-level;
- covered / uncovered / partially covered.

Examples:

| Business case | Recommended representation |
|---------------|----------------------------|
| Open surface parking | `ParkingLayout = openSpace`, `NumberOfParkingLevels = 1` |
| Covered single-level parking | `ParkingLayout = covered`, `NumberOfParkingLevels = 1` |
| Multi-storey parking building | `ParkingLayout = multistorey`, `NumberOfParkingLevels > 1` |

---

### Ownership and management

Ownership and management are distinct concepts.

A P+R may be owned by different entities, including:

- CFL;
- Ponts et Chaussées;
- Fonds du Rail;
- a municipality.

The manager may be CFL or another entity.

A parking may also be partially managed by CFL. In such cases, the CFL-managed part SHALL be represented as an identifiable `ParkingArea` when the data allows it.

Example: in Diekirch, CFL manages approximately 100 places through the P+R application out of a total of approximately 650 places. The CFL-managed part should therefore be represented as a distinct `ParkingArea` of the physical parking.

This allows the model to distinguish:

- the total physical capacity of the parking;
- the capacity managed by CFL;
- the access rules applicable to the CFL-managed part;
- the responsibility for management and data provision.

---

### Parking place categories and capacity

The CFL profile supports capacity modelling by category of parking places.

Identified physical categories include:

| Category | Description | Modelling note |
|----------|-------------|----------------|
| Standard | Regular parking places | Included in the general physical capacity. |
| PMR | Places reserved for persons with reduced mobility | Physical category. May be represented using `ParkingUserType = impairedMobility` when the capacity is known. |
| Chargy | Electric vehicle charging places | Physical category. Should be represented using recharging-related NeTEx capacity or property fields. |
| Family | Wider family places, for example at Belval | Physical category when such places are explicitly identified. |
| Flex | Places or usage related to Flex for private individuals | Exists in all P+R. Should be represented as a physical category only when the places are physically identifiable. |
| CFlex | Places or usage related to CFlex for CFL agents | Physical CFlex places are currently identified only at Luxembourg Gare P+R, with 6 spaces. |
| Police / Customs | Reserved and clearly identified places | Should be represented only when they correspond to physical reserved places. |

EWAP and P+R Pass are not parking place categories. They are access rights or quota-based entitlements and SHALL NOT be included in physical category capacity.

Kiss & Rail is not a parking place category. It is a tariff rule corresponding to a free short-stay period of 30 minutes in the CFL P+R tariff context.

Capacity MAY be provided:

- globally at `Parking` level;
- by physical level, using `ParkingArea`;
- by clearly identified managed perimeter, using `ParkingArea`;
- by physical place category, within the relevant `Parking` or `ParkingArea`.

The recommended modelling granularity is therefore:

```text
Parking
→ ParkingArea / physical level, where relevant
→ Physical place category, where available and publishable
→ Capacity
```

Internal quotas such as EWAP and P+R Pass SHALL remain outside the published physical capacity model.

Individual parking spaces are not modelled in the CFL base profile.

If individual parking spaces need to be represented in a later extension, `ParkingBay` may be used.

---

### Kiss & Rail and drop-off scope

Kiss & Rail SHALL NOT be modelled as a standalone `Parking` or as a `ParkingArea` in the CFL parking profile.

In the CFL business context, Kiss & Rail is understood as a tariff rule corresponding to a free short-stay period of 30 minutes in the CFL P+R tariff context. It does not correspond to a specific physical set of parking spaces.

The Kiss & Rail rule SHALL therefore be treated as tariff information associated with a parking, not as:

- a parking type;
- a physical parking area;
- a parking place category;
- a reserved capacity;
- an access entitlement.

Physical drop-off areas may exist near stations. However, according to the current business scope, these areas are managed by the city and not by CFL.

They SHALL therefore remain outside the CFL-managed parking profile unless a future scope extension explicitly requires city-managed or externally managed drop-off facilities to be represented.

If such external drop-off facilities are introduced in a later extension, they SHALL be clearly distinguished from CFL-managed P+R parkings and from the Kiss & Rail tariff rule.

---

### Access control, payment and reservation

A `Parking` MAY include standard NeTEx information about payment and reservation facilities.

In the CFL P+R context, the payment model is one of the following:

| Business case | Interpretation | Recommended NeTEx representation |
|--------------|----------------|----------------------------------|
| Free parking | No payment is required | `ParkingPaymentProcess = free` |
| Payment via the P+R application | Payment and access are handled through the P+R app. This implies a barrier and a camera reading the licence plate. | `ParkingPaymentProcess = payByMobileDevice payByPlate`, and `PaymentByMobile` where app/payment URL information is available |
| Payment at the payment terminal | Used for users who have not installed the P+R app. The hourly tariff applies. | `ParkingPaymentProcess` should indicate a payment-required process; the detailed hourly tariff is handled in the fare model |

In the CFL profile, payment via the P+R application is understood as a combined business process covering:

- app-based payment;
- controlled access through a barrier;
- licence plate recognition.

These elements SHALL NOT be modelled as separate parking types or physical subdivisions.

The physical access-control equipment MAY be documented where the appropriate NeTEx equipment structure is confirmed, but the main information retained in the parking profile is the payment/access process.

For CFL P+R parkings, `ParkingReservation` SHALL be set to `noReservations` when the information is published, because there is never a guaranteed reservation of an individual parking space.

EWAP and P+R Pass SHALL NOT be represented as `ParkingArea`, `ParkingBay`, reserved spaces or published physical capacity.

They are access rights or quota-based entitlements. Their detailed modelling belongs to the fare / access-right model or to internal access-control systems.

Internal quotas associated with EWAP or P+R Pass SHALL NOT be published as physical capacity in the `SiteFrame`.

The detailed hourly tariff, the conditional free-parking rule and the geographic eligibility zone SHALL be handled in the future NeTEx fare model, not in the physical parking structure.

---

### Equipment and internal access

A Parking may include internal equipment or infrastructure elements, such as:

- lift;
- stairs;
- ramp.

These elements may be relevant for accessibility, internal navigation and access to parking levels or zones.

In the MVP, such elements may be optional and provided only when available.

---

### Counting, availability and smartparking

The term **smartparking** is treated as a usage label or technical qualification, not as a functional parking type.

A Parking may be considered a smartparking when it is equipped with intelligent counting or availability equipment.

The following counting configurations are identified in the CFL context:

| Counting configuration | Description |
|------------------------|-------------|
| No counting equipment | No automated counting or availability equipment is available. |
| Camera-based counting with parking map | Cameras are associated with a map of the parking places. |
| Dynamic entry / exit counting | Counting is performed at entry and exit barriers, with increment / decrement logic. This may also support identification of the vehicle country of origin for statistical purposes. |

Some parkings may also have a local display showing the number of remaining available places.

Where known, the model should represent:

- whether availability data exists;
- whether availability is available globally or by physical level;
- the counting method;
- whether level-based availability is directly measured or calculated;
- whether a remaining-places display exists;
- the approximate update frequency.

In the CFL P+R context, dynamic availability may be available by floor.

In NeTEx, the presence of real-time occupancy data should be represented using `RealTimeOccupancyAvailable` when applicable.

If individual bays are monitored, `MonitoredBays` may be used in `ParkingProperties`. However, individual `ParkingBay` objects are not modelled by default in the CFL base profile.

---

### Elements and attributes retained in the CFL profile

| Element / Attribute | Description | Cardinality (CFL profile) | Notes / Constraints |
|---------------------|-------------|----------------------------|---------------------|
| `@id` | Identifier of the Parking | 1..1 | Must follow CFL identifier scheme. |
| `Name` | Public name or label of the parking | 1..1 | Should be understandable for passengers and operators. |
| `Description` | Passenger-facing or operational description | 0..1 | Optional. |
| `Centroid/Location` | Geographic coordinates, WGS84 | 1..1 | Mandatory longitude / latitude. |
| `ParkingType` | Standard NeTEx parking type | 0..1 | SHALL be used when a suitable standard value exists, e.g. `parkAndRide` for P+R. |
| `TypeOfParkingRef` | CFL-specific parking type reference | 0..1 | References a shared `TypeOfParking` in `resource.xml` when no suitable standard value exists or when CFL requires an internal functional classification, e.g. Bikebox. Kiss & Rail SHALL NOT be represented as a `TypeOfParkingRef`. |
| `ParkingLayout` | Physical layout of the parking | 0..1 | Used to describe open, covered, multi-storey, underground, etc. layouts. |
| `NumberOfParkingLevels` | Number of levels | 0..1 | Should be provided for multi-level parkings and when known. |
| `TotalCapacity` | Total number of places | 0..1 | Should be provided when known and stable. |
| `PrincipalCapacity` | Main usable capacity | 0..1 | May be used when relevant. |
| `parkingAreas/ParkingArea` | Physical or clearly bounded management subdivisions | 0..n | Used primarily for physical levels when capacity or availability is known by level, or for a clearly identified CFL-managed perimeter. SHALL NOT be used for tariff rules, access rights, internal quotas or non-physical entitlements. |
| `parkingProperties/ParkingProperties` | Additional parking properties | 0..n | Used for vehicle types, secure parking, max stay, monitored bays, etc. |
| `ParkingVehicleTypes` | Vehicle types allowed | 0..1 | Used to distinguish cars, cycles, e-bikes, etc. |
| `ParkingStayList` | Type of parking stay | 0..1 | May be used only when the parking has an actual stay-related property to publish. SHALL NOT be used to model Kiss & Rail as a physical parking type. |
| `MaximumStay` | Maximum permitted stay | 0..1 | Used only when a maximum physical stay rule is directly applicable to the parking object. Detailed tariff rules, such as the first 30 minutes free, belong to the future fare model. |
| `RechargingAvailable` | Indicates whether vehicle recharging is available | 0..1 | Relevant for Chargy areas or parking places. |
| `NumberOfBaysWithRecharging` | Number of bays with charging facilities | 0..1 | May be used at `ParkingArea` level. |
| `RealTimeOccupancyAvailable` | Indicates whether occupancy data is available | 0..1 | Used for smartparking / availability data. |
| `MonitoredBays` | Indicates whether individual bays are monitored | 0..1 | Used only when such information exists. |
| `ParkingPaymentProcess` | Payment model | 0..1 | Used when the payment model is known. In the CFL P+R context, the payment model is either free, payment via the P+R app, or payment at the payment terminal. |
| `ParkingReservation` | Reservation availability | 0..1 | For CFL P+R parkings, SHALL indicate that no reservation of an individual parking space is available when this information is published. SHALL NOT be used to model P+R Pass, EWAP or internal quotas. |
| `BookingUrl` | URL for booking or registration | 0..1 | Used only when a reliable booking or registration URL exists, e.g. for Bikebox registration where applicable. SHALL NOT be used to imply that P+R spaces can be reserved. |
| `placeEquipments` | Equipment container for the parking | 0..1 | Optional; used when equipment information is available. |
| `placeEquipments/PassengerSafetyEquipment` | Safety-related equipment information | 0..n | May be used for safety equipment, such as CCTV. |
| `placeEquipments/VehicleReleaseEquipment` | Access-control-related equipment information | 0..n | May be used for access or release equipment, such as remote control. |

Note: in the CFL P+R context, payment via the P+R app is understood as a combined payment and access process. It implies controlled access through a barrier and licence plate recognition. These mechanisms are not modelled as parking types, `ParkingArea` elements or physical capacity.

---

### CFL-specific modelling rules

- Parkings are published under `SiteFrame/.../parkings`, currently in `parking.xml`.
- `ParkingType` SHALL be used when a suitable standard NeTEx value exists, e.g. `parkAndRide` for P+R.
- `TypeOfParkingRef` SHALL be used when no suitable standard `ParkingType` value exists or when a CFL-specific functional classification is explicitly required.
- When used, `TypeOfParkingRef` MUST point to a shared `TypeOfParking` value published in `resource.xml`.
- `Centroid/Location` MUST be provided.
- `TotalCapacity` SHOULD be provided whenever the physical capacity is known and stable.
- P+R and Bikebox are modelled as parking types in the CFL profile.
- Kiss & Rail SHALL NOT be modelled as a parking type, `ParkingArea` or physical parking place category. It is a tariff rule corresponding to a free short-stay period of 30 minutes in the CFL P+R tariff context.
- Physical drop-off areas managed by the city are outside the CFL-managed parking scope unless a future scope extension explicitly includes them.
- Smartparking is not a functional parking type; it is a technical qualification derived from counting or availability equipment.
- A Parking MAY be subdivided into `ParkingArea` elements when needed to represent physical levels or clearly bounded management perimeters.
- `ParkingArea` SHALL NOT be used for tariff rules, access rights, internal quotas or non-physical entitlements.
- Individual parking spaces are not modelled by default.
- `ParkingBay` is not retained in the CFL base profile, but may be used in a later extension if individual spaces need to be represented. This is not the case for EWAP or P+R Pass.
- For multi-level parkings, capacity SHOULD be provided by level when the data is available.
- Category-based capacities MAY be provided within the relevant `Parking` or level-based `ParkingArea`, when the information is available and intended for publication.
- For partially CFL-managed parkings, the CFL-managed part SHOULD be represented as a distinct `ParkingArea` only when the managed perimeter is clearly identifiable.
- In the CFL P+R context, the payment model is either free, payment via the P+R app, or payment at the payment terminal.
- Payment via the P+R app implies controlled access through a barrier and licence plate recognition.
- Payment at the payment terminal applies the hourly tariff for users who do not use the P+R app.
- EWAP and P+R Pass are access rights or quota-based entitlements. They SHALL NOT be represented as physical capacity, `ParkingArea`, `ParkingBay` or reserved spaces.
- Internal quotas associated with EWAP or P+R Pass SHALL NOT be published as customer-facing parking capacity.
- There is no guaranteed reservation of an individual parking space in the CFL P+R context.

---

### Minimal XML example: P+R parking

⚠️ *Illustrative only — not real CFL production data.*

```xml
<Parking id="LU:CFL:Parking:PR001" version="1">
  <Name>Station X - P+R</Name>
  <Centroid>
    <Location>
      <Longitude>6.1000</Longitude>
      <Latitude>49.6000</Latitude>
    </Location>
  </Centroid>
  <ParkingType>parkAndRide</ParkingType>
  <ParkingLayout>openSpace</ParkingLayout>
  <NumberOfParkingLevels>1</NumberOfParkingLevels>
  <TotalCapacity>650</TotalCapacity>
</Parking>
```
---

### Minimal XML example: partially managed P+R parking

⚠️ *Illustrative only — not real CFL production data.*

```xml
<Parking id="LU:CFL:Parking:Diekirch" version="1">
  <Name>Diekirch - P+R</Name>
  <Centroid>
    <Location>
      <Longitude>6.1550</Longitude>
      <Latitude>49.8670</Latitude>
    </Location>
  </Centroid>
  <ParkingType>parkAndRide</ParkingType>
  <TotalCapacity>650</TotalCapacity>

  <parkingAreas>
    <ParkingArea id="LU:CFL:ParkingArea:Diekirch:CFLManaged" version="1">
      <Name>Diekirch - CFL managed P+R area</Name>
      <TotalCapacity>100</TotalCapacity>
    </ParkingArea>
  </parkingAreas>
</Parking>
```
---
### Minimal XML example: parking with availability data

⚠️ *Illustrative only — not real CFL production data.*

```xml
<Parking id="LU:CFL:Parking:PR002" version="1">
  <Name>Station Y - P+R</Name>
  <Centroid>
    <Location>
      <Longitude>6.2000</Longitude>
      <Latitude>49.7000</Latitude>
    </Location>
  </Centroid>
  <ParkingType>parkAndRide</ParkingType>
  <TotalCapacity>300</TotalCapacity>
  <RealTimeOccupancyAvailable>true</RealTimeOccupancyAvailable>
</Parking>
```
---

### Illustrative XML example: P+R parking with level-based ParkingAreas and category-based capacity

⚠️ *Illustrative only — not real CFL production data.*

⚠️ *This example illustrates the intended modelling pattern. The exact XML structure for `ParkingProperties/spaces/ParkingCapacity` must be validated against the CEN NeTEx XSD and the CFL implementation profile before being used as a production pattern.*

This example shows how `ParkingArea` may be used to describe a multi-level P+R parking without modelling individual parking spaces.

It illustrates:

- a total parking capacity at `Parking` level;
- one `ParkingArea` per physical level;
- category-based capacities expressed inside the relevant level area;
- PMR places represented through `ParkingUserType = impairedMobility`;
- charging places represented through `NumberOfSpacesWithRechargePoint`;
- no individual `ParkingBay` modelling.

The capacity interpretation is:

```text
Parking TotalCapacity = Level 0 TotalCapacity + Level 1 TotalCapacity
280 = 140 + 140
```

Category-based capacities are included in the level capacities. They are not added on top of them.

```xml
<Parking id="LU:CFL:Parking:PR003" version="1">
  <Name>Station Z - P+R</Name>
  <Centroid>
    <Location>
      <Longitude>6.2500</Longitude>
      <Latitude>49.7500</Latitude>
    </Location>
  </Centroid>

  <ParkingType>parkAndRide</ParkingType>
  <ParkingLayout>multistorey</ParkingLayout>
  <NumberOfParkingLevels>2</NumberOfParkingLevels>
  <TotalCapacity>280</TotalCapacity>
  <ParkingReservation>noReservations</ParkingReservation>
  <RechargingAvailable>true</RechargingAvailable>

  <parkingAreas>
    <ParkingArea id="LU:CFL:ParkingArea:PR003:Level0" version="1">
      <Name>Station Z - P+R - Level 0</Name>
      <TotalCapacity>140</TotalCapacity>

      <parkingProperties>
        <ParkingProperties id="LU:CFL:ParkingProperties:PR003:Level0:PMR" version="1">
          <Name>Level 0 - PMR places</Name>
          <ParkingUserTypes>impairedMobility</ParkingUserTypes>
          <spaces>
            <ParkingCapacity id="LU:CFL:ParkingCapacity:PR003:Level0:PMR" version="1">
              <ParkingUserType>impairedMobility</ParkingUserType>
              <NumberOfSpaces>4</NumberOfSpaces>
            </ParkingCapacity>
          </spaces>
        </ParkingProperties>

        <ParkingProperties id="LU:CFL:ParkingProperties:PR003:Level0:Charging" version="1">
          <Name>Level 0 - charging places</Name>
          <spaces>
            <ParkingCapacity id="LU:CFL:ParkingCapacity:PR003:Level0:Charging" version="1">
              <NumberOfSpacesWithRechargePoint>8</NumberOfSpacesWithRechargePoint>
            </ParkingCapacity>
          </spaces>
        </ParkingProperties>
      </parkingProperties>
    </ParkingArea>

    <ParkingArea id="LU:CFL:ParkingArea:PR003:Level1" version="1">
      <Name>Station Z - P+R - Level 1</Name>
      <TotalCapacity>140</TotalCapacity>

      <parkingProperties>
        <ParkingProperties id="LU:CFL:ParkingProperties:PR003:Level1:PMR" version="1">
          <Name>Level 1 - PMR places</Name>
          <ParkingUserTypes>impairedMobility</ParkingUserTypes>
          <spaces>
            <ParkingCapacity id="LU:CFL:ParkingCapacity:PR003:Level1:PMR" version="1">
              <ParkingUserType>impairedMobility</ParkingUserType>
              <NumberOfSpaces>4</NumberOfSpaces>
            </ParkingCapacity>
          </spaces>
        </ParkingProperties>

        <ParkingProperties id="LU:CFL:ParkingProperties:PR003:Level1:Family" version="1">
          <Name>Level 1 - family places</Name>
          <ParkingUserTypes>families</ParkingUserTypes>
          <spaces>
            <ParkingCapacity id="LU:CFL:ParkingCapacity:PR003:Level1:Family" version="1">
              <ParkingUserType>families</ParkingUserType>
              <NumberOfSpaces>6</NumberOfSpaces>
            </ParkingCapacity>
          </spaces>
        </ParkingProperties>
      </parkingProperties>
    </ParkingArea>
  </parkingAreas>
</Parking>
```

In this example, `ParkingArea` is used for the physical subdivision by level.

The category-specific capacities are expressed inside the relevant level area. They are therefore interpreted as subsets of the level capacity, not as additional capacities.

The total capacity of the parking is obtained by summing only the level capacities:

```text
TotalCapacity = Level 0 + Level 1
280 = 140 + 140
```

The PMR, charging and family capacities provide additional detail within each level.

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

The `Name` of a bikebox parking SHALL contain the public or operational label of the bikebox location.

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
| `Name/Text` | 1..1 | Public or operational bikebox location label, e.g. `LUX01 - Sandweiler-Contern, Gare Nord`. |
| `Description/Text` | 1..1 | Current standard description of secure access conditions. |
| `Centroid/Location/Longitude` | 1..1 | Mandatory WGS84 longitude. |
| `Centroid/Location/Latitude` | 1..1 | Mandatory WGS84 latitude. |
| `placeEquipments/PassengerSafetyEquipment` | 1..1 | Safety equipment container currently provided for each bikebox. |
| `placeEquipments/PassengerSafetyEquipment/Cctv` | 1..1 | Current value: `false`. |
| `placeEquipments/VehicleReleaseEquipment` | 1..1 | Access-control equipment container currently provided for each bikebox. |
| `placeEquipments/VehicleReleaseEquipment/RemoteControl` | 1..1 | Current value: `false`. |
| `TypeOfParkingRef` | 1..1 | Must reference `LU:CFL:TypeOfParking:BIKEBOX` with `versionRef="1"`. |
| `TotalCapacity` | 1..1 | Current value: `32` for the bikeboxes shown in the current dataset. |
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



