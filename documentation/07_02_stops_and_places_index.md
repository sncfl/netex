# 7.2 Stops and places entities

# StopPlace (SiteFrame)

## Functional description

A **StopPlace** represents a physical location where passengers may access the public transport system (rail station, funicular station, shuttle stop).  
It serves as the **spatial anchor** for all stop-related elements referenced across the dataset.

A StopPlace is the authoritative source for:
- The official name of the station or stop,
- Its coordinates,
- Its internal components (Quays, Entrances, etc.),
- Its public-facing identifiers.

StopPlaces are defined **exclusively** in the *SiteFrame* and referenced by other frames (ServiceFrame, TimetableFrame).

---

## Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|-------------------------|----------------------|
| `@id` | Identifier of the StopPlace | 1..1 | Must follow CFL identifier scheme (see 5.1). |
| `Name` | Public name of the stop or station | 1..1 | Name may contain one or more &lt;Text&gt; elements with xml:lang, allowing multilingual names. |
| `ShortName` | Optional short label | 0..1 | Used only if stable and meaningful. |
| `PublicCode` | Public-facing code | 0..1 | Unique if present (e.g. mnemonic code). |
| `Centroid/Location` | Geographic coordinates (WGS84) | 1..1 | Mandatory latitude/longitude. |
| `StopPlaceType` | Type of location | 1..1 | Typically `railStation`, `multimodalStopPlace`, etc. |
| `Quays` | List of quays belonging to the StopPlace | 1..n | At least one quay required. |
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

All other frames reference these elements through identifiers, ensuring consistency across the entire dataset.


---

# Quay (SiteFrame)

## Functional description

A **Quay** represents a physical boarding or alighting point within a StopPlace.  
For rail, this corresponds to a **platform** (e.g., “Platform 1”), a boarding edge, or a clearly identifiable passenger access point.

A Quay is:
- A **child element of a StopPlace**,
- The **physical reference** used by JourneyPatterns and VehicleJourneys,
- The element referenced through `QuayRef` in the ServiceFrame and TimetableFrame.

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
- A Quay is referenced by `QuayRef` in:
  - `StopPointInJourneyPattern`  
  - `VehicleJourneyStopAssignment` (future WP1 usage)

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
    <ParentSiteRef ref="LU:CFL:StopPlace:LuxGare"/>
</Quay>
```

## Usage in other NeTEx frames

### In the ServiceFrame

A Quay is referenced via:
- `StopPointInJourneyPattern/QuayRef`
- `VehicleJourneyStopAssignment/QuayRef`

Example:

```xml
<StopPointInJourneyPattern id="LU:CFL:StopPointInJourneyPattern:LuxGare" version="1">
    <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
    <QuayRef ref="LU:CFL:Quay:LuxGare-1"/>
</StopPointInJourneyPattern>

```

### In the Timetable

A Quay may also be referenced when assigning a specific platform to a VehicleJourney at a given stop.

Example:
```xml
<VehicleJourneyStopAssignment id="LU:CFL:VehicleJourneyStopAssignment:LuxGare" version="1">
    <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
    <QuayRef ref="LU:CFL:Quay:LuxGare-1"/>
</VehicleJourneyStopAssignment>
```

---

## Additional rules and constraints (CFL MVP)

- If the public platform name changes, the **Quay ID must remain unchanged**.
- Coordinates (if provided) must follow WGS-84 and represent the accessible edge of the platform.
- Recommended `QuayType` for CFL rail: **`platform`**.

---

## Summary of CFL MVP restrictions

- Quays are mandatory within the StopPlace structure.
- No equipment or accessibility features in MVP.
- No detailed geometry required (option in WP3).
- Use of multilingual <Text> names encouraged when available.
- Stable and deterministic identifier scheme required.

---

# ScheduledStopPoint (ServiceFrame)

## Purpose and scope

A **ScheduledStopPoint** represents a *logical stop* within a journey pattern, i.e.  a point where a vehicle may stop to allow passengers to board or alight.

It is:

- The **operational stop reference** used by JourneyPatterns and VehicleJourneys;
- Linked to the physical stop via **QuayRef** (platform) or **StopPlaceRef** (station);
- The *abstract stop* that appears in timetables, prior to assignment to a real platform.

A ScheduledStopPoint is **not** a physical object.  

It exists to model:

- The ordered sequence of stops in a ServiceJourneyPattern,
- The target commercial stop (station/board point) independently of the platform,
- The binding between timetable data and infrastructure data.

In the CFL MVP:

- Each ScheduledStopPoint SHALL reference exactly one StopPlace or Quay. 
- Referencing a StopPlace is the typical case for CFL rail services today, but referencing a Quay MAY be used in future extensions or for modes requiring platform-level granularity. The CFL profile therefore keeps both options open, even though StopPlaceRef will be by far the dominant case in current timetable data.
- The ScheduledStopPoint is the **bridge** between operational schedules (VehicleJourneys) and the infrastructure model (SiteFrame).

---

## Modelling principles

### Logical vs Physical

A ScheduledStopPoint is *logical*:

- It identifies **where** a train stops in a commercial sequence (e.g., “Luxembourg”),
- Not **how** or **where exactly** on a platform it stops.

Physical details (platform assignment) are handled via:

- `QuayRef` inside `VehicleJourneyStopAssignment`; or
- `QuayRef` inside `StopPointInJourneyPattern` if the assignment is stable.

### Identifier strategy and rationale

In the CFL profile, ScheduledStopPoint identifiers are intentionally designed to be **human-readable** and derived from public station name (e.g. “LuxGare”), rather than using opaque technical codes such as `SSP0001`.

This approach is chosen for several reasons:

- **Debuggability and maintainability**  
  ScheduledStopPoints appear extensively in timetables (TimetableFrame,VehicleJourney, StopPointInJourneyPattern).  
  Readable identifiers make it significantly easier for analysts, developers and operational staff to interpret timetable structures directly from XML files, logs, diffs or diagnostic tools, without requiring a lookup table.

- **Operational transparency**  
  Many CFL teams (planning, SIV, operational support) consult or manipulate
  timetable data.  
  Using meaningful identifiers allows non-technical users to immediately recognise stations when inspecting exports or troubleshooting integrations.

- **Stability and long-term maintainability**  
  Unlike StopPlace identifiers, which will eventually be aligned with the future national stop register, ScheduledStopPoints are internal timetable objects.
  Giving them human-readable identifiers ensures long-term clarity and avoids spreading opaque or temporary codes into downstream systems.

- **Alignment with other CFL identifiers**  
  The CFL MVP already uses recognisable patterns for StopPlace (`LuxGare`) and
  Quay (`LuxGare-1`).  
  Using the same strategy for ScheduledStopPoint keeps the profile consistent and predictable across frames.

Therefore, the identifier pattern adopted in the CFL profile is:

Pattern:

`LU:CFL:ScheduledStopPoint:<ReadableStationId>`

where `<ReadableStationId>` is a short, human-friendly identifier derived from the public station name (e.g. “LuxGare”, “EschAlzette”, “Bettembourg”), and **not** the technical identifier of the StopPlace object.

Examples:

- `LU:CFL:ScheduledStopPoint:LuxGare`
- `LU:CFL:ScheduledStopPoint:EschAlzette`
- `LU:CFL:ScheduledStopPoint:Bettembourg`

There is no requirement nor expectation for the ScheduledStopPoint identifier to match or derive from the StopPlace identifier. The two identifiers serve different purposes.

**Important note:** Although the identifiers of the ScheduleStopPoint is designed to be human-readable for data production purposes, it should not be used for semantic search or analytics on the data consumer side. Data consumers should always consider identifiers as a "black-box" chain of characters used to build references and share information across files in the dataset.

---

## Elements and attributes retained in the CFL MVP

| Element / Attribute       | Description                                   | Cardinality (CFL MVP) | Notes / Constraints                                                | Example value |
|---------------------------|-----------------------------------------------|------------------------|--------------------------------------------------------------------|----------------|
| `@id`                     | Unique identifier of the ScheduledStopPoint   | 1..1                   | Human-readable and derived from the public station label.          | `LU:CFL:ScheduledStopPoint:LuxGare` |
| `version`                 | Object version                                | 1..1                   | Incremented only when the semantic meaning of the stop changes.   | `1` |
| `Name`                    | Human-readable stop name                      | 1..1                   | Multilingual `<Text>` elements allowed.                           | `Luxembourg` |
| `StopPlaceRef`            | Reference to the physical StopPlace           | 0..1                   | Typical case for CFL rail services. SHALL be present unless `QuayRef` is used. | `LU:CFL:StopPlace:LuxGare` |
| `QuayRef`                 | Reference to a specific Quay (platform)       | 0..1                   | MAY be used if the ScheduledStopPoint is defined at platform level. `StopPlaceRef` and `QuayRef` SHALL NOT both be present. | — |
| 

### Notes

- Exactly **one** of `StopPlaceRef` or `QuayRef` SHALL be present.
- For CFL rail timetables, `StopPlaceRef` is expected to be the **dominant case**, but `QuayRef` remains available for future extensions or modes requiring platform-level granularity.
- ScheduledStopPoint does **not** carry any geographic coordinates; location is inherited from the referenced StopPlace or Quay.
- The name of the ScheduledStopPoint SHOULD reflect the public-facing name of the stop and remain consistent with SiteFrame definitions.

---

## Rules and cardinalities

| Relationship / Rule                                   | Cardinality | Description and LU-specific constraints |
|--------------------------------------------------------|-------------|------------------------------------------|
| ScheduledStopPoint → `StopPlaceRef`                    | 0..1        | A ScheduledStopPoint MAY reference a StopPlace. SHALL be present unless `QuayRef` is used. |
| ScheduledStopPoint → `QuayRef`                         | 0..1        | A ScheduledStopPoint MAY reference a Quay. SHALL NOT be present if `StopPlaceRef` is used. |
| `StopPlaceRef` XOR `QuayRef`                           | Exactly one | Exactly one of the two SHALL be present for each ScheduledStopPoint. |
| ServiceJourneyPattern → ScheduledStopPoint             | 1..*        | A JourneyPattern SHALL reference one or more ScheduledStopPoints in its ordered sequence. |
| StopPointInJourneyPattern → ScheduledStopPoint         | 1..1        | Each StopPointInJourneyPattern SHALL reference exactly one ScheduledStopPoint. |
| VehicleJourneyStopAssignment → ScheduledStopPoint      | 1..1        | Each vehicle stop assignment must reference the corresponding logical ScheduledStopPoint. |

### Notes

- The ScheduledStopPoint defines the *logical stop* used in journey patterns and timetables; physical and operational details are handled through StopPlace and Quay objects.
- Ordering of stops is always defined at the level of `StopPointInJourneyPattern`, never on the ScheduledStopPoint itself.

---

## XML example

The example below illustrates a typical CFL ScheduledStopPoint referencing a StopPlace.
It uses human-readable identifiers as defined in the CFL conventions.

```xml
<ScheduledStopPoint id="LU:CFL:ScheduledStopPoint:LuxGare" version="1">
    <Name>
        <Text xml:lang="fr">Luxembourg</Text>
        <Text xml:lang="de">Luxemburg</Text>
        <Text xml:lang="lb">Lëtzebuerg</Text>
    </Name>

    <StopPlaceRef ref="LU:CFL:StopPlace:SP00001"/>
</ScheduledStopPoint>
```



