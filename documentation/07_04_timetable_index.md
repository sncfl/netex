# 7.4 Timetable entities

---

## Scope (CFL MVP)

This section documents the entities used in `line_<LineId>.xml` files to describe:
- the **service structure** (ServiceFrame), and
- the **timetabled content** (TimetableFrame).

Calendar primitives (`DayType`, `OperatingPeriod`, `DayTypeAssignment`) are shared reference entities defined in `resource.xml` and documented in **07_05_shared_reference_entities**.

---

## StopPointInJourneyPattern (ServiceFrame)

### Purpose and scope

A **StopPointInJourneyPattern** represents the *ordered occurrence* of a `ScheduledStopPoint` within a `ServiceJourneyPattern` (pattern of VehicleJourneys).  
It defines the structural path of a commercial service variant.

It is used to express:

- The **sequence** of logical stops,
- The **reference** to the underlying `ScheduledStopPoint`,
- An optional **stable platform assignment** (`QuayRef`), if and only if all VehicleJourneys using the `ServiceJourneyPattern` share the same platform at that stop.

A StopPointInJourneyPattern does **not** contain any timing information.  
It purely describes the **structure** of the journey.

---

### Modelling principles

#### Structural component of a ServiceJourneyPattern

A `ServiceJourneyPattern` is composed of an ordered list of `StopPointInJourneyPattern` elements.  
Each element references a logical stop (`ScheduledStopPointRef`).
Logical stop definitions (`ScheduledStopPoint`) and the logical-to-physical mapping (`PassengerStopAssignment`) are documented in [07_02_stops_and_places_index](07_02_stops_and_places_index.md).

NeTEx v2.0 favours the natural ordering of XML, which means that the order in which each `StopPointInJourneyPattern` is listed defines the order in which they are served by the `ServiceJourneyPattern` (i.e., its stop sequence).

#### Platform assignment

In the CFL MVP:

- Platform assignment may be known **per journey** → handled via `VehicleJourneyStopAssignment` (optional).
- `QuayRef` MAY appear in the StopPointInJourneyPattern **only when the platform is structurally constant** for all VehicleJourneys using that `ServiceJourneyPattern`.
- This is not typical for CFL rail operations today.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute     | Description                                 | Cardinality (CFL MVP) | Notes / Constraints                                                   | Example value |
|-------------------------|---------------------------------------------|------------------------|-----------------------------------------------------------------------|--------------|
| `@id`                   | Identifier of the StopPointInJourneyPattern | 1..1                   | Must be stable and unique in the CFL codespace.                       | `LU:CFL:SPJP:LuxGare_1` |
| `ScheduledStopPointRef` | Reference to the logical ScheduledStopPoint | 1..1                   | Mandatory. Identifies the commercial stop.                            | `LU:CFL:ScheduledStopPoint:LuxGare` |
| `QuayRef`               | Stable platform reference                   | 0..1                   | Only if all journeys share the same stable platform for this pattern. | — |

---

### Rules and cardinalities

| Relationship / Rule                               | Cardinality | Description |
|---------------------------------------------------|-------------|-------------|
| ServiceJourneyPattern → StopPointInJourneyPattern | 1..*        | A `ServiceJourneyPattern` SHALL contain one or more `StopPointInJourneyPattern` elements in ordered sequence (natural XML order). |
| StopPointInJourneyPattern → ScheduledStopPoint    | 1..1        | Each element SHALL reference exactly one `ScheduledStopPoint`. |
| StopPointInJourneyPattern → QuayRef               | 0..1        | MAY be present only if the quay assignment is structurally constant. |

---

### XML examples

#### Example 1 — Typical CFL case (no stable platform assignment)


```xml
<ServiceJourneyPattern id="LU:CFL:SJP:LUX-ESCH_v1" version="1">
    <pointsInSequence>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:LuxGare_1" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:Bettembourg_2" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:Bettembourg"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:EschAlzette_3" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:EschAlzette"/>
        </StopPointInJourneyPattern>

    </pointsInSequence>
</ServiceJourneyPattern>
```

### Example 2 - With stable platform (illustrative only)

```xml
<ServiceJourneyPattern id="LU:CFL:SJP:LUX-HWD_v1" version="1">
    <pointsInSequence>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:LuxGare_1" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
            <QuayRef ref="LU:CFL:Quay:LuxGare-3A"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:Howald_2" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:Howald"/>
            <QuayRef ref="LU:CFL:Quay:Howald-2"/>
        </StopPointInJourneyPattern>

    </pointsInSequence>
</ServiceJourneyPattern>
```

---

## ServiceJourneyPattern (ServiceFrame)

### Purpose and scope

A **ServiceJourneyPattern** represents a *directional commercial pattern* used by a family of VehicleJourneys.  
It defines the ordered sequence of logical stops (ScheduledStopPoints) that characterise one variant of the service offer.

A ServiceJourneyPattern:

- Is **directional** (e.g. Luxembourg → Esch-sur-Alzette);
- Groups all VehicleJourneys that share the same stopping pattern;
- Includes an ordered list of StopPointInJourneyPattern elements;
- May define a stable platform assignment when applicable.

It does **not** contain times; these belong to VehicleJourney and TimetabledPassingTime.

In the CFL MVP:

- Each Line SHALL contain at least **two** ServiceJourneyPatterns  
  → one per direction (outbound / inbound).
- Additional ServiceJourneyPatterns SHALL be created for each distinct commercial
  variant (e.g. via Howald, limited-stop services, short-turn variants).

---

### Modelling principles

#### Directionality
A ServiceJourneyPattern is always **one-way**, representing a single direction of travel. A bidirectional Line therefore requires at least two patterns.

#### Variants
Distinct patterns SHALL be created when:

- Intermediate stops differ,
- A “via” alternative exists (e.g. via Howald),
- Some trains skip stops,
- Short-turn services terminate earlier.

#### Relationship with the Line
All ServiceJourneyPatterns of a given Line MUST:

- Begin and end at stops belonging to that Line,
- Correspond to the commercial scope of the Line,
- Be directionally aligned with the Line's terminals.

#### Platform assignment
Platform assignment is normally **dynamic**, handled through `VehicleJourneyStopAssignment`.  
`QuayRef` MAY appear inside `StopPointInJourneyPattern` **only if stable** across all VehicleJourneys using the pattern.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute  | Description                           | Cardinality (CFL MVP) | Notes / Constraints                              | Example value |
|----------------------|---------------------------------------|------------------------|---------------------------------------------------|---------------|
| `@id`                | Identifier of the ServiceJourneyPattern | 1..1                 | Stable and unique in the CFL codespace            | `LU:CFL:SJP:LUX-ESCH_v1` |
| `version`            | Object version                         | 1..1                  | Incremented when the stop sequence changes        | `1` |
| `Name`               | Human-readable pattern name            | 1..1                  | Multilingual `<Text>` allowed                     | `Luxembourg → Esch-sur-Alzette` |
| `DirectionType`      | Travel direction                       | 0..1                  | Optional; may support filtering                   | `outbound` |
| `pointsInSequence`   | Ordered StopPointInJourneyPattern list | 1..1                  | SHALL contain at least one element                | — |

---

### Rules and cardinalities

| Relationship / Rule                              | Cardinality | Description |
|--------------------------------------------------|-------------|-------------|
| Line → ServiceJourneyPattern                      | 2..*        | A Line SHALL contain at least two patterns (one per direction). |
| ServiceJourneyPattern → StopPointInJourneyPattern | 1..*        | Ordered list defining the stop sequence (natural XML order). |
| StopPointInJourneyPattern → ScheduledStopPoint    | 1..1        | Each occurrence references exactly one logical stop. |
| Stable platform assignment                        | 0..1 (per stop) | `QuayRef` MAY be present only when constant across all VehicleJourneys using the pattern. |

---

### XML examples

#### Example — Outbound direction (Luxembourg → Esch-sur-Alzette)


```xml
<ServiceJourneyPattern id="LU:CFL:SJP:LUX-ESCH_v1" version="1">
    <Name>
        <Text xml:lang="fr">Luxembourg → Esch-sur-Alzette</Text>
    </Name>

    <pointsInSequence>
        <StopPointInJourneyPattern id="LU:CFL:SPJP:LuxGare_1" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:Bettembourg_2" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:Bettembourg"/>
        </StopPointInJourneyPattern>

        <StopPointInJourneyPattern id="LU:CFL:SPJP:EschAlzette_3" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:EschAlzette"/>
        </StopPointInJourneyPattern>
    </pointsInSequence>
</ServiceJourneyPattern>
```

---

## VehicleJourney (TimetableFrame)

### Purpose and scope

A **VehicleJourney** represents a *scheduled theoretical train run* applying to one or several days of operation. It combines:

- A **ServiceJourneyPattern** (direction + stop sequence);
- **timetabled passing times** at each stop,
- The **calendar of operation** through `DayTypeRef`,
- Optional commercial or operational identifiers.

It is the core scheduled entity used by downstream systems (SIV, timetable publication, journey planners, and real-time SIRI-ET/SIRI-VM integration).

A VehicleJourney does **not** represent the actual run of the day (handled by `DatedVehicleJourney`). It represents the *template* applicable on the days defined by its `DayTypeRef`.

---

### Modelling principles

#### Link to the ServiceJourneyPattern
A VehicleJourney SHALL reference exactly one ServiceJourneyPattern, which defines:

- The ordered list of ScheduledStopPoints,
- The direction of travel,
- The variant of the service (e.g. via Howald, limited stops).

#### Timetabled times
The VehicleJourney provides theoretical times:

- `DepartureTime` (optional at the origin),
- `ArrivalTime` (optional at the terminus).

Each `TimetabledPassingTime` references a `StopPointInJourneyPattern` element, and their order is inherited from the ServiceJourneyPattern structure (i.e., it should be exactly the same).

#### Calendar of operation
Operating days are defined through one or more `DayTypeRef` elements.

Calendar primitives (`DayType`, `OperatingPeriod`, `DayTypeAssignment`) are defined in `resource.xml` and documented in **07_05_shared_reference_entities**.

Separate VehicleJourneys SHALL be created when:

- Running days differ (e.g., weekdays VS weekends);
- The stop pattern changes;
- The times differ.

#### Identifiers
A VehicleJourney identifier SHALL be:

- Stable;
- Unique in the CFL codespace;
- Preferably human-readable and meaningful for analysts.

Example convention:

`LU:CFL:VJ:<TrainNumber>_<Origin>-<Destination>`

#### Commercial codes
If a public train number exists (e.g. RE407), it SHOULD be represented through `PublicCode`.

#### Operator and mode
In the CFL MVP:

- `OperatorRef` SHALL reference `LU:CFL:Operator:CFL`.
- `TransportMode` SHALL be `rail`.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute        | Description                                   | Cardinality (CFL MVP) | Notes / Constraints                                 | Example value |
|----------------------------|-----------------------------------------------|------------------------|------------------------------------------------------|--------------|
| `@id`                      | Unique identifier of the VehicleJourney       | 1..1                   | Stable CFL identifier                                | `LU:CFL:VJ:RE407_LUX-ESCH` |
| `version`                  | Object version                                | 1..1                   | Incremented when timetabled times change             | `1` |
| `Name`                     | Human-readable name                           | 0..1                   | Optional                                             | `RE 407` |
| `PublicCode`               | Commercial train number                       | 0..1                   | Recommended when available                           | `RE407` |
| `ServiceJourneyPatternRef` | Reference to the stopping pattern             | 1..1                   | Mandatory                                            | `LU:CFL:SJP:LUX-ESCH_v1` |
| `DayTypeRef`               | Calendar of operation                         | 1..*                   | Mandatory                                            | `LU:CFL:DayType:WEEKDAYS` |
| `OperatorRef`              | Operating company                             | 1..1                   | CFL-specific                                         | `LU:CFL:Operator:CFL` |
| `TransportMode`            | Mode of transport                             | 1..1                   | Always `rail` for CFL                                | `rail` |
| `passingTimes`             | List of stop times                            | 1..1                   | One block per stop in the pattern                    | — |

---

### Rules and cardinalities

| Relationship / Rule                   | Cardinality | Description |
|----------------------------------------|-------------|-------------|
| VehicleJourney → ServiceJourneyPattern | 1..1        | Each journey SHALL refer to exactly one SJP. |
| VehicleJourney → DayTypeRef            | 1..*        | At least one calendar MUST be defined. |
| VehicleJourney → passingTimes          | 1..1        | A complete list of passing times SHALL be provided. |
| TimetabledPassingTime → DepartureTime  | 0..1        | Optional at the first stop. |
| TimetabledPassingTime → ArrivalTime    | 0..1        | Optional at the final stop. |
| PublicCode                             | 0..1        | SHOULD be used when a commercial train number exists. |

---

### XML example

#### Example — RE 407 (Luxembourg → Esch-sur-Alzette)

```xml
<VehicleJourney id="LU:CFL:VJ:RE407_LUX-ESCH" version="1">
    <Name>
        <Text xml:lang="fr">RE 407</Text>
    </Name>
    <PublicCode>RE407</PublicCode>

    <ServiceJourneyPatternRef ref="LU:CFL:SJP:LUX-ESCH_v1"/>
    <OperatorRef ref="LU:CFL:Operator:CFL"/>
    <TransportMode>rail</TransportMode>

    <DayTypeRef ref="LU:CFL:DayType:WEEKDAYS"/>

    <passingTimes>

        <TimetabledPassingTime>
            <StopPointInJourneyPatternRef ref="LU:CFL:SPJP:LuxGare_1"/>
            <DepartureTime>08:15:00</DepartureTime>
        </TimetabledPassingTime>

        <TimetabledPassingTime>
            <StopPointInJourneyPatternRef ref="LU:CFL:SPJP:Bettembourg_2"/>
            <ArrivalTime>08:23:00</ArrivalTime>
            <DepartureTime>08:24:00</DepartureTime>
        </TimetabledPassingTime>

        <TimetabledPassingTime>
            <StopPointInJourneyPatternRef ref="LU:CFL:SPJP:EschAlzette_3"/>
            <ArrivalTime>08:31:00</ArrivalTime>
        </TimetabledPassingTime>

    </passingTimes>
</VehicleJourney>
```

---

## TimetabledPassingTime (TimetableFrame)

### Purpose and scope

A **TimetabledPassingTime** provides the *scheduled theoretical time* at which a VehicleJourney is planned to arrive at and/or depart from a specific stop (as defined in the referenced StopPointInJourneyPattern).

It contains:

- The scheduled **ArrivalTime**;
- The scheduled **DepartureTime**;
- And the reference to the stop in the journey pattern.

In the CFL MVP:

- Every VehicleJourney SHALL contain one TimetabledPassingTime per stop.
- Only theoretical times are included; real-time updates belong to SIRI-ET/VM.

---

### Modelling principles

#### One passing time per stop
For each StopPointInJourneyPattern in the ServiceJourneyPattern, the corresponding VehicleJourney SHALL include exactly one TimetabledPassingTime.

#### Arrival vs departure times
- At the **origin stop**: only `DepartureTime` is normally present.  
- At the **terminus**: only `ArrivalTime` is normally present.  
- At **intermediate stops**: both MAY be present.

#### Theoretical data only
TimetabledPassingTime represents *planned schedule*, not real-time updates.

#### No platform assignment inside this element
Platform (Quay) assignment is modelled via **VehicleJourneyStopAssignment**, not here.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Card. (MVP) | Notes / Constraints | Example |
|---------------------|-------------|-------------|----------------------|---------|
| `StopPointInJourneyPatternRef` | Reference to the stop in the pattern | 1..1 | Mandatory | `LU:CFL:SPJP:LuxGare_1` |
| `ArrivalTime` | Scheduled arrival time | 0..1 | Optional at first stop | `08:23:00` |
| `DepartureTime` | Scheduled departure time | 0..1 | Optional at last stop | `08:24:00` |

---

### Rules and cardinalities

| Rule | Description |
|------|-------------|
| One TimetabledPassingTime per stop | The number of passingTimes MUST match the number of stops in the pattern. |
| Arrival/Departure optionality | Origin: no ArrivalTime. Terminus: no DepartureTime. |
| Time format | SHALL use `HH:MM:SS` (ISO 8601). |
| No platform assignment | QuayRef SHALL NOT appear here. |

---

### XML example

```xml
<TimetabledPassingTime>
    <StopPointInJourneyPatternRef ref="LU:CFL:SPJP:LuxGare_1"/>
    <DepartureTime>08:15:00</DepartureTime>
</TimetabledPassingTime>

<TimetabledPassingTime>
    <StopPointInJourneyPatternRef ref="LU:CFL:SPJP:Bettembourg_2"/>
    <ArrivalTime>08:23:00</ArrivalTime>
    <DepartureTime>08:24:00</DepartureTime>
</TimetabledPassingTime>

<TimetabledPassingTime>
    <StopPointInJourneyPatternRef ref="LU:CFL:SPJP:EschAlzette_3"/>
    <ArrivalTime>08:31:00</ArrivalTime>
</TimetabledPassingTime>
```

---


## VehicleJourneyStopAssignment (TimetableFrame)

### Purpose and scope

A **VehicleJourneyStopAssignment** links a *logical stop* of a VehicleJourney (`ScheduledStopPoint`) to a *physical boarding location* (`Quay` or `StopPlace`), for that specific VehicleJourney.

It is used to express:

- The **platform** (`Quay`) planned for a given stop of a VehicleJourney;
- The **station context** when needed (`StopPlaceRef`).

In the CFL MVP:

- VehicleJourneyStopAssignment is **optional**.
- It is used when platform information is part of the published timetable or needed by downstream systems (e.g., SIV).
- Real-time platform changes are out of scope and handled by SIRI-VM / SIRI-ET.

Assignments are modelled **inside the VehicleJourney**, under `stopAssignments`.

---

### Modelling principles

#### Link between timetable and infrastructure

VehicleJourneyStopAssignment acts as a bridge between:

- The **timetable layer** (VehicleJourney, ScheduledStopPoint);
- The **infrastructure layer** (Quay, StopPlace in the SiteFrame).

The stop infrastructure (`StopPlace`, `Quay`) and the mapping from `ScheduledStopPoint` via `PassengerStopAssignment` are documented in [07_02_stops_and_places_index](07_02_stops_and_places_index.md).

#### Scope of use in the CFL MVP

- MAY be used when CFL wishes to communicate planned platforms.
- SHOULD be omitted when platforms are considered too volatile or only available in real time.

#### Reference strategy

In the CFL MVP:

- `ScheduledStopPointRef` is used to identify the logical stop.
- `QuayRef` is used when the planned platform is known.
- `StopPlaceRef` MAY be added when needed, but is not mandatory (it can be inferred from the Quay).

Assignments are associated to a VehicleJourney through containment (`stopAssignments`), not via an explicit VehicleJourneyRef.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute     | Description                         | Card. (MVP) | Notes / Constraints                           | Example value |
|-------------------------|-------------------------------------|-------------|-----------------------------------------------|--------------|
| `@id`                   | Identifier of the stop assignment   | 1..1        | Stable within CFL codespace                   | `LU:CFL:VJSA:RE407_LuxGare` |
| `version`               | Object version                      | 1..1        | Incremented when the assignment changes       | `1` |
| `ScheduledStopPointRef` | Reference to the logical stop       | 1..1        | MUST reference an existing ScheduledStopPoint | `LU:CFL:ScheduledStopPoint:LuxGare` |
| `QuayRef`               | Reference to the physical platform  | 0..1        | Used when a planned platform is available     | `LU:CFL:Quay:LuxGare-3A` |
| `StopPlaceRef`          | Reference to the station            | 0..1        | Optional; can often be inferred from Quay     | `LU:CFL:StopPlace:SP00001` |

---

### Rules and cardinalities

| Rule | Description |
|------|-------------|
| Container | VehicleJourneyStopAssignment elements SHALL appear inside `VehicleJourney/stopAssignments`. |
| One assignment per (VehicleJourney, stop) | At most one assignment SHOULD exist per VehicleJourney and ScheduledStopPoint. |
| ScheduledStopPointRef | SHALL reference an existing ScheduledStopPoint used in the VehicleJourney’s pattern. |
| QuayRef | MAY be present when a planned platform is part of the timetable offer. |
| StopPlaceRef | MAY be omitted when the station can be inferred from the Quay. |

---

### XML example


```xml
<VehicleJourney id="LU:CFL:VJ:RE407_LUX-ESCH" version="1">
    <!-- Other VehicleJourney elements (Name, PublicCode, SJP ref, DayTypeRef, passingTimes, ...) -->

    <stopAssignments>
        <VehicleJourneyStopAssignment id="LU:CFL:VJSA:RE407_LuxGare" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:LuxGare"/>
            <StopPlaceRef ref="LU:CFL:StopPlace:SP00001"/>
            <QuayRef ref="LU:CFL:Quay:LuxGare-3A"/>
        </VehicleJourneyStopAssignment>

        <VehicleJourneyStopAssignment id="LU:CFL:VJSA:RE407_Bettembourg" version="1">
            <ScheduledStopPointRef ref="LU:CFL:ScheduledStopPoint:Bettembourg"/>
            <StopPlaceRef ref="LU:CFL:StopPlace:SP00002"/>
            <QuayRef ref="LU:CFL:Quay:Bettembourg-2"/>
        </VehicleJourneyStopAssignment>
    </stopAssignments>
</VehicleJourney>
```




