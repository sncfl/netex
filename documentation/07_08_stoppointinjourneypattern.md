# 7.8 StopPointInJourneyPattern (ServiceFrame)

## 7.8.1 Purpose and scope

A **StopPointInJourneyPattern** represents the *ordered occurrence* of a ScheduledStopPoint
within a ServiceJourneyPattern. It defines the structural path of a commercial service variant.

It is used to express:

- the **sequence** of logical stops,
- the **reference** to the underlying ScheduledStopPoint,
- an optional **stable platform assignment** (QuayRef), if and only if all VehicleJourneys
  using the ServiceJourneyPattern share the same platform at that stop.

A StopPointInJourneyPattern does **not** contain any timing information.  
It purely describes the **structure** of the journey.

---

## 7.8.2 Modelling principles

### Structural component of a ServiceJourneyPattern
A ServiceJourneyPattern is composed of an ordered list of StopPointInJourneyPattern elements.
Each element:

- identifies a logical stop (`ScheduledStopPointRef`),
- specifies its position in the sequence via `order`.

### Ordering
The attribute `order`:

- SHALL start at `1`,
- SHALL be strictly increasing,
- determines the stop sequence for the ServiceJourneyPattern.

### Platform assignment
In the CFL MVP:

- Platform assignment is **normally dynamic** → handled in `VehicleJourneyStopAssignment`.
- `QuayRef` MAY appear in the StopPointInJourneyPattern **only when the platform is constant** for all trains using that ServiceJourneyPattern.
- This is not typical for CFL rail operations today.

---

## 7.8.3 Elements and attributes retained in the CFL MVP

| Element / Attribute        | Description                                        | Cardinality (CFL MVP) | Notes / Constraints                                                   | Example value |
|----------------------------|----------------------------------------------------|-------------------------|-----------------------------------------------------------------------|----------------|
| `@id`                      | Identifier of the StopPointInJourneyPattern        | 1..1                    | Must be stable and unique in the CFL codespace.                      | `LU:CFL:SPJP:LuxGare_1` |
| `@order`                   | Position in the stop sequence                      | 1..1                    | Integer ≥1, strictly increasing across the ServiceJourneyPattern.           | `1` |
| `ScheduledStopPointRef`    | Reference to the logical ScheduledStopPoint        | 1..1                    | Mandatory. Identifies the commercial stop.                           | `LU:CFL:ScheduledStopPoint:LuxGare` |
| `QuayRef`                  | Stable platform reference                          | 0..1                    | Only if all trains share the same stable platform for this pattern.  | — |

---

## 7.8.4 Rules and cardinalities

| Relationship / Rule                         | Cardinality | Description |
|---------------------------------------------|-------------|-------------|
| JourneyPattern → StopPointInJourneyPattern  | 1..*        | A ServiceJourneyPattern SHALL contain one or more StopPointInJourneyPattern elements in ordered sequence. |
| StopPointInJourneyPattern → ScheduledStopPoint | 1..1      | Each element SHALL reference exactly one ScheduledStopPoint. |
| StopPointInJourneyPattern → QuayRef         | 0..1        | MAY be present only if the quay assignment is structurally constant. |
| `order` attribute                           | 1..1        | SHALL be strictly increasing to define the stop sequence. |

---

## 7.8.5 XML examples

### Example 1 — Typical CFL case (no stable platform assignment)

```xml
<ServiceJourneyPattern id="LU:CFL:JP:LUX-ESCH" version="1">
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

### Example 2 - With stable platform (illustrative only)

```xml
<ServiceJourneyPattern id="LU:CFL:JP:LUX-HWD" version="1">
    <pointsInSequence>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:LuxGare_1" version="1" order="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
            <QuayRef ref="LU:CFL:Quay:LuxGare-3A"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:Howald_2" version="1" order="2">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:Howald"/>
            <QuayRef ref="LU:CFL:Quay:Howald-2"/>
        </StopPointInJourneyPattern>

    </pointsInSequence>
</ServiceJourneyPattern>
```

