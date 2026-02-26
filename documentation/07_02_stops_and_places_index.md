# 7.2 Stops and places entities

---

# StopPlace (SiteFrame)

## Functional description

A **StopPlace** represents a physical location where passengers may access the public transport system (rail station, funicular station, shuttle stop).  
It serves as the **spatial anchor** for all stop-related elements referenced across the dataset.

A StopPlace is the authoritative source for:
- The official name of the station or stop,
- Its coordinates,
- Its internal components (Quays, Entrances, etc.),
- Its public-facing identifiers.

StopPlaces are defined **exclusively** in the *SiteFrame* and referenced by the ServiceFrame through `PassengerStopAssignment` (see **PassengerStopAssignment (ServiceFrame)** below).  
They may also be referenced indirectly when journey-level quay/platform assignment is published (via `VehicleJourneyStopAssignment`, optional in MVP).

---

## Elements and attributes retained in the CFL MVP

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

### Notes
- Attributes not listed above are not used in the CFL MVP.
- Additional details (equipment, entrances, accessibility) may be added in later work packages.

---

## CFL-specific modelling rules

### Names
- **Name** must reflect the official published station name.  
- Accents and hyphens are allowed.  
- If multilingual names are provided, they must be equivalent.

### Identifiers
- Must follow the format:  
  **`LU:CFL:StopPlace:<LocalId>`**  
  e.g., `LU:CFL:StopPlace:SP00032`.

- Identifiers never change, even when the name is updated.

### Coordinates
- Coordinates must be WGS84 decimal degrees.  
- Coordinates represent the *logical centre* of the station (not the platform).

### Quays
- Every StopPlace must contain **at least one** Quay.  
- Quays must be defined within the same SiteFrame.  
- Quay identifiers must be globally unique.

---

## Minimal XML example (illustrative only)

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

## Summary

The StopPlace is the foundational spatial object of the CFL timetable dataset.
It provides the authoritative definition of each station or stop, including:

- Stable identifier,
- Public name(s),
- Coordinates,
- Associated quays.

Other frames resolve StopPlaces through references, primarily via PassengerStopAssignment, ensuring consistency across the entire dataset.

---

# Quay (SiteFrame)

## Functional description

A **Quay** represents a physical boarding or alighting point within a StopPlace.  
For rail, this corresponds to a **platform** (e.g., “Platform 1”), a boarding edge, or a clearly identifiable passenger access point.

A Quay is:
- A **child element of a StopPlace**,
- A physical infrastructure object referenced when quay-level precision is required,
- Referenced via `QuayRef` in assignment structures (PassengerStopAssignment optional; VehicleJourneyStopAssignment optional)..

Each Quay belongs to **exactly one** StopPlace.

---

## Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|-------------------------|----------------------|
| `@id` | Unique identifier of the Quay | 1..1 | Must follow CFL identifier scheme. |
| `Name` | Public platform/stop name | 1..1 | Multilingual `<Text>` elements allowed. |
| `PublicCode` | Public-facing platform code | 0..1 | If used, must be unique within the StopPlace. |
| `Description` | Additional textual info | 0..1 | Optional; avoid operational notes. |
| `Centroid/Location` | Coordinates of the quay | 0..1 | Optional in MVP; may be added in WP3. |
| `ParentSiteRef` | Reference to parent StopPlace | 1..1 | Must reference the enclosing StopPlace. |
| `QuayType` | Type of quay or platform | 0..1 | If used: `platform`, `bay`, etc. (NeTEx enumeration values). |

### Notes
- In the MVP, equipment (signage, shelter, accessibility features) is **not** included.  
  These elements may be provided in WP3 “Accessibility”.
- A Quay must always be part of a StopPlace structure.

---

## CFL-specific modelling rules

### Names
- **Name** must correspond to the public-facing platform or boarding label.  
  Examples: “Platform 1”, “Platform 3B”.  
- Multilingual representations may be provided via multiple `<Text>` elements.

### Identifiers
- Must follow the format:  
  **`LU:CFL:Quay:<LocalId>`**  
  e.g., `LU:CFL:Quay:LuxGare-1`.

- Identifiers must be:
  - Globally unique,  
  - Stable across updates,  
  - Unchanged if the platform is renamed.

### Relationship to StopPlace
- A Quay **must** have a `ParentSiteRef` pointing to its StopPlace.  
- Every StopPlace must contain one or more Quays.

---

## Minimal XML example (illustrative only)

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

## Usage in other NeTEx frames

### Stable logical-to-physical mapping (ServiceFrame)

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

### Journey-level quay/platform assignment (TimetableFrame, optional)

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

## Summary of CFL MVP restrictions

- Quays are mandatory within the StopPlace structure.
- No equipment or accessibility features in MVP.
- No detailed geometry required (option in WP3).
- Use of multilingual <Text> names encouraged when available.
- Stable and deterministic identifier scheme required.

---
# PassengerStopAssignment (ServiceFrame)

## Purpose and scope

A **PassengerStopAssignment** links a logical stop used in the service structure (`ScheduledStopPoint`) to the physical stop infrastructure (`StopPlace`, and optionally `Quay`).

It is the mechanism used in the CFL MVP to express the difference between:
- **logical stop points** (service design), and
- **physical stop places / boarding locations** (infrastructure).

PassengerStopAssignment is used to:
- map each logical stop (`ScheduledStopPoint`) to the physical station/stop area (`StopPlaceRef`);
- optionally specify a stable boarding location (`QuayRef`) when this assignment is considered structurally stable.

---

## Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|---------------------|
| `@id` | Identifier of the PassengerStopAssignment | 1..1 | Must follow CFL identifier scheme. |
| `ScheduledStopPointRef` | Reference to the logical stop point | 1..1 | References a `ScheduledStopPoint` defined in the ServiceFrame. |
| `StopPlaceRef` | Reference to the physical StopPlace | 1..1 | References a `StopPlace` defined in the SiteFrame. |
| `QuayRef` | Reference to the physical Quay | 0..1 | Optional. Used only when a stable quay-level assignment is published. |

### Notes
- `StopPlaceRef` is mandatory in the MVP.
- `QuayRef` is optional and should not be used for information that varies per journey; use `VehicleJourneyStopAssignment` for that case.

---

## CFL-specific modelling rules

- Each `ScheduledStopPoint` SHALL have exactly one `PassengerStopAssignment` linking it to a `StopPlaceRef`.
- `QuayRef` MAY be provided only when the quay/platform assignment is considered stable in published data.
- When quay assignment is known per journey (and may vary), it SHALL be modelled via `VehicleJourneyStopAssignment` (optional in MVP), not via `PassengerStopAssignment`.

---

## Minimal XML example (illustrative only)

```xml
<PassengerStopAssignment id="LU:CFL:PassengerStopAssignment:LuxGare" version="1">
    <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
    <StopPlaceRef ref="LU:CFL:StopPlace:SP00032"/>
</PassengerStopAssignment>
```

---
# ScheduledStopPoint (ServiceFrame)

## Purpose and scope

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

## Modelling principles

### Logical vs Physical

A ScheduledStopPoint is *logical*:

- It identifies **where** a service stops in a commercial sequence (e.g., “Luxembourg”),
- Not **where exactly** a passenger boards (platform) or the physical geometry of the stop.

Physical details are handled via:
- `PassengerStopAssignment` (stable mapping to `StopPlace` / optional `Quay`), and
- `VehicleJourneyStopAssignment` when the quay/platform is known per journey (optional).

---

## Identifier strategy and rationale

In the CFL profile, `ScheduledStopPoint` identifiers are intentionally designed to be **human-readable** and derived from the public station name (e.g. “LuxGare”), rather than using opaque technical codes such as `SSP0001`.

This approach is chosen for several reasons:

### Debuggability and maintainability
ScheduledStopPoints appear extensively in timetables (ServiceFrame patterns and TimetableFrame structures).  
Readable identifiers make it significantly easier for analysts, developers and operational staff to interpret timetable structures directly from XML files, logs, diffs or diagnostic tools, without requiring a lookup table.

### Operational transparency
Many CFL teams (planning, SIV, operational support) consult or manipulate timetable data.  
Using meaningful identifiers allows non-technical users to immediately recognise stations when inspecting exports or troubleshooting integrations.

### Stability and long-term maintainability
Unlike StopPlace identifiers, which may be aligned with a future national stop register, ScheduledStopPoints are internal timetable objects.  
Giving them human-readable identifiers ensures long-term clarity and avoids spreading opaque or temporary codes into downstream systems.

### Alignment with other CFL identifiers
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

## Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints | Example value |
|---------------------|-------------|------------------------|---------------------|--------------|
| `@id` | Unique identifier of the ScheduledStopPoint | 1..1 | Human-readable and derived from the public station label. | `LU:CFL:ScheduledStopPoint:LuxGare` |
| `version` | Object version | 1..1 | Incremented only when the semantic meaning of the stop changes. | `1` |
| `Name` | Human-readable stop name | 1..1 | Multilingual `<Text>` elements allowed. | `Luxembourg` |

### Notes
- ScheduledStopPoint does not carry geographic coordinates; location is resolved via the mapped StopPlace/Quay.
- The mapping to physical infrastructure is expressed via PassengerStopAssignment.

---

## Rules and cardinalities

| Relationship / Rule | Cardinality | Description and LU-specific constraints |
|---------------------|-------------|------------------------------------------|
| ScheduledStopPoint → PassengerStopAssignment | 1..1 | Each ScheduledStopPoint SHALL be mapped to exactly one StopPlace via PassengerStopAssignment. |
| PassengerStopAssignment → StopPlaceRef | 1..1 | StopPlaceRef is mandatory in MVP. |
| PassengerStopAssignment → QuayRef | 0..1 | Optional. Used only when stable quay assignment is published. |
| VehicleJourneyStopAssignment → QuayRef | 0..1 | Optional. Used when quay/platform is known per journey. |
| StopPointInJourneyPattern → ScheduledStopPointRef | 1..1 | Each StopPointInJourneyPattern SHALL reference exactly one ScheduledStopPoint. |

### Notes
- The ScheduledStopPoint defines the logical stop used in patterns and timetables.
- Ordering of stops is defined at the level of StopPointInJourneyPattern, never on the ScheduledStopPoint itself.

---

## XML example

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
