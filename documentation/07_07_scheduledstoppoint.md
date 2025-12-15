# 7.7 ScheduledStopPoint (ServiceFrame)

## 7.7.1 Purpose and scope

A **ScheduledStopPoint** represents a *logical stop* within a journey pattern, i.e.  a point where a vehicle may stop to allow passengers to board or alight.

It is:

- the **operational stop reference** used by JourneyPatterns and VehicleJourneys,
- linked to the physical stop via **QuayRef** (platform) or **StopPlaceRef** (station),
- the *abstract stop* that appears in timetables, prior to assignment to a real platform.

A ScheduledStopPoint is **not** a physical object.  

It exists to model:

- the ordered sequence of stops in a ServiceJourneyPattern,
- the target commercial stop (station/board point) independently of the platform,
- the binding between timetable data and infrastructure data.

In the CFL MVP:

- Each ScheduledStopPoint SHALL reference exactly one StopPlace or Quay. 
- Referencing a StopPlace is the typical case for CFL rail services today, but
referencing a Quay MAY be used in future extensions or for modes requiring
platform-level granularity. The CFL profile therefore keeps both options open,
even though StopPlaceRef will be by far the dominant case in current timetable
data.

- The ScheduledStopPoint is the **bridge** between operational schedules (VehicleJourneys) and the infrastructure model (SiteFrame).

---

## 7.7.2 Modelling principles

### Logical vs Physical

A ScheduledStopPoint is *logical*:

- it identifies **where** a train stops in a commercial sequence (e.g. “Luxembourg”),
- not **how** or **where exactly** on a platform it stops.

Physical details (platform assignment) are handled via:

- `QuayRef` inside `VehicleJourneyStopAssignment`,
- or `QuayRef` inside `StopPointInJourneyPattern` if the assignment is stable.

### Identifier strategy and rationale

In the CFL profile, ScheduledStopPoint identifiers are intentionally designed to be
**human-readable** and derived from public station name (e.g. “LuxGare”), rather than using opaque technical
codes such as `SSP0001`.

This approach is chosen for several reasons:

- **Debuggability and maintainability**  
  ScheduledStopPoints appear extensively in timetables (TimetableFrame,
  VehicleJourney, StopPointInJourneyPattern).  
  Readable identifiers make it significantly easier for analysts, developers and
  operational staff to interpret timetable structures directly from XML files,
  logs, diffs or diagnostic tools, without requiring a lookup table.

- **Operational transparency**  
  Many CFL teams (planning, SIV, operational support) consult or manipulate
  timetable data.  
  Using meaningful identifiers allows non-technical users to immediately recognise
  stations when inspecting exports or troubleshooting integrations.

- **Stability and long-term maintainability**  
  Unlike StopPlace identifiers, which will eventually be aligned with the future
  national stop register, ScheduledStopPoints are internal timetable objects.
  Giving them human-readable identifiers ensures long-term clarity and avoids
  spreading opaque or temporary codes into downstream systems.

- **Alignment with other CFL identifiers**  
  The CFL MVP already uses recognisable patterns for StopPlace (`LuxGare`) and
  Quay (`LuxGare-1`).  
  Using the same strategy for ScheduledStopPoint keeps the profile consistent and
  predictable across frames.

Therefore, the identifier pattern adopted in the CFL profile is:

Pattern:

`LU:CFL:ScheduledStopPoint:<ReadableStationId>`

where `<ReadableStationId>` is a short, human-friendly identifier derived from the
public station name (e.g. “LuxGare”, “EschAlzette”, “Bettembourg”), and **not**
the technical identifier of the StopPlace object.

Examples:

- `LU:CFL:ScheduledStopPoint:LuxGare`
- `LU:CFL:ScheduledStopPoint:EschAlzette`
- `LU:CFL:ScheduledStopPoint:Bettembourg`

There is no requirement nor expectation for the ScheduledStopPoint identifier to match or derive from the StopPlace identifier. The two identifiers serve different purposes.

---

## 7.7.3 Elements and attributes retained in the CFL MVP

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

## 7.7.4 Rules and cardinalities

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

## 7.7.5 XML example

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

