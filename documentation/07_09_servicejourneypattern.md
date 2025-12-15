# 7.9 ServiceJourneyPattern (ServiceFrame)

## 7.9.1 Purpose and scope

A **ServiceJourneyPattern** represents a *directional commercial pattern* used by a
family of ServiceJourneys. It defines the ordered sequence of logical stops
(ScheduledStopPoints) that characterise one variant of the service offer.

A ServiceJourneyPattern:

- is **directional** (e.g. Luxembourg → Esch-sur-Alzette),
- groups all ServiceJourneys that share the same stopping pattern,
- includes an ordered list of StopPointInJourneyPattern elements,
- may define a stable platform assignment when applicable.

It does **not** contain times; these belong to ServiceJourney or VehicleJourney.

In the CFL MVP:

- Each Line SHALL contain at least **two** ServiceJourneyPatterns  
  → one per direction (outbound / inbound).
- Additional ServiceJourneyPatterns SHALL be created for each distinct commercial
  variant (e.g. via Howald, limited-stop services, short-turn variants).

---

## 7.9.2 Modelling principles

### Directionality
A ServiceJourneyPattern is always **one-way**, representing a single direction
of travel. A bidirectional Line therefore requires at least two patterns.

### Variants
Distinct patterns SHALL be created when:

- intermediate stops differ,
- a “via” alternative exists (e.g. via Howald),
- some trains skip stops,
- short-turn services terminate earlier.

### Relationship with the Line
All ServiceJourneyPatterns of a given Line MUST:

- begin and end at stops belonging to that Line,
- correspond to the commercial scope of the Line,
- be directionally aligned with the Line's terminals.

### Platform assignment
Platform assignment is normally **dynamic**, handled through
VehicleJourneyStopAssignment.  
`QuayRef` MAY appear inside StopPointInJourneyPattern **only if stable** across all
ServiceJourneys using the pattern.

---

## 7.9.3 Elements and attributes retained in the CFL MVP

| Element / Attribute     | Description                             | Cardinality (CFL MVP) | Notes / Constraints                               | Example value |
|-------------------------|-------------------------------------------|------------------------|----------------------------------------------------|---------------|
| `@id`                   | Identifier of the ServiceJourneyPattern   | 1..1                   | Stable and unique in the CFL codespace            | `LU:CFL:SJP:LUX-ESCH_v1` |
| `version`               | Object version                            | 1..1                   | Incremented when the stop sequence changes        | `1` |
| `Name`                  | Human-readable pattern name               | 1..1                   | Multilingual `<Text>` allowed                     | `Luxembourg → Esch-sur-Alzette` |
| `DirectionType`         | Travel direction                          | 0..1                   | Optional; may support filtering                   | `outbound` |
| `pointsInSequence`      | Ordered StopPointInJourneyPattern list    | 1..1                   | SHALL contain at least one element                | — |

---

## 7.9.4 Rules and cardinalities

| Relationship / Rule                                 | Cardinality | Description |
|------------------------------------------------------|-------------|-------------|
| Line → ServiceJourneyPattern                         | 2..*        | A Line SHALL contain at least two patterns (one per direction). |
| ServiceJourneyPattern → StopPointInJourneyPattern    | 1..*        | Ordered list defining the stop sequence. |
| StopPointInJourneyPattern → ScheduledStopPoint       | 1..1        | Each occurrence references exactly one logical stop. |
| `order` attribute                                    | 1..1        | MUST be strictly increasing within the pattern. |
| Stable platform assignment                           | 0..1 (per stop) | `QuayRef` MAY be present only when constant across all services of the pattern. |

---

## 7.9.5 XML examples

### Example 1 — Outbound direction (Luxembourg → Esch-sur-Alzette)

```xml
<ServiceJourneyPattern id="LU:CFL:SJP:LUX-ESCH_v1" version="1">
    <Name>
        <Text xml:lang="fr">Luxembourg → Esch-sur-Alzette</Text>
    </Name>

    <pointsInSequence>
        <StopPointInJourneyPattern id="LU:CFL:SPJP:LuxGare_1" version="1" order="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:Bettembourg_2" version="1" order="2">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:Bettembourg"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:EschAlzette_3" version="1" order="3">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:EschAlzette"/>
        </StopPointInJourneyPattern>
    </pointsInSequence>
</ServiceJourneyPattern>
```

