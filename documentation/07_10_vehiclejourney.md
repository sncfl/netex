# 7.10 VehicleJourney (ServiceFrame)

## 7.10.1 Purpose and scope

A **VehicleJourney** represents a *scheduled theoretical train run* applying to one or several days of operation. It combines:

- A **ServiceJourneyPattern** (direction + stop sequence);
- **timetabled passing times** at each stop,
- The **calendar of operation** through DayTypeRef,
- Optional commercial or operational identifiers.

It is the core scheduled entity used by downstream systems (SIV, timetable publication, journey planners, and real-time SIRI-ET/SIRI-VM integration).

A VehicleJourney does **not** represent the actual run of the day (handled by `DatedVehicleJourney`). It represents the *template* applicable on the days defined by its DayTypeRef.

---

## 7.10.2 Modelling principles

### Link to the ServiceJourneyPattern
A VehicleJourney SHALL reference exactly one ServiceJourneyPattern, which defines:

- The ordered list of ScheduledStopPoints,
- The direction of travel,
- The variant of the service (e.g. via Howald, limited stops).

### Timetabled times
The VehicleJourney provides theoretical times:

- `DepartureTime` (optional at the origin),
- `ArrivalTime` (optional at the terminus).

Each `TimetabledPassingTime` references a `StopPointInJourneyPattern` element, and their order is inherited from the ServiceJourneyPattern structure (i.e., it should be exactly the same).

### Calendar of operation
Operating days are defined through one or more `DayTypeRef` elements.

Separate VehicleJourneys SHALL be created when:

- Running days differ (e.g., weekdays v.s weekends);
- The stop pattern changes;
- The times differ.

### Identifiers
A VehicleJourney identifier SHALL be:

- Stable;
- Unique in the CFL codespace;
- Preferably human-readable and meaningful for analysts.

Example convention:

`LU:CFL:VJ:<TrainNumber>_<Origin>-<Destination>`

### Commercial codes
If a public train number exists (e.g. RE407), it SHOULD be represented through `PublicCode`.

### Operator and mode
In the CFL MVP:

- `OperatorRef` SHALL reference `LU:CFL:Operator:CFL`.
- `TransportMode` SHALL be `rail`.

---

## 7.10.3 Elements and attributes retained in the CFL MVP

| Element / Attribute        | Description                                   | Cardinality (CFL MVP) | Notes / Constraints                                 | Example value |
|----------------------------|-----------------------------------------------|-------------------------|------------------------------------------------------|----------------|
| `@id`                      | Unique identifier of the VehicleJourney       | 1..1                    | Stable CFL identifier                                | `LU:CFL:VJ:RE407_LUX-ESCH` |
| `version`                  | Object version                                | 1..1                    | Incremented when timetabled times change            | `1` |
| `Name`                     | Human-readable name                           | 0..1                    | Optional                                             | `RE 407` |
| `PublicCode`               | Commercial train number                       | 0..1                    | Recommended when available                           | `RE407` |
| `ServiceJourneyPatternRef` | Reference to the stopping pattern             | 1..1                    | Mandatory                                            | `LU:CFL:SJP:LUX-ESCH_v1` |
| `DayTypeRef`               | Calendar of operation                         | 1..*                    | Mandatory                                            | `LU:CFL:DayType:WEEKDAYS` |
| `OperatorRef`              | Operating company                             | 1..1                    | CFL-specific                                         | `LU:CFL:Operator:CFL` |
| `TransportMode`            | Mode of transport                             | 1..1                    | Always `rail` for CFL                                | `rail` |
| `passingTimes`             | List of stop times                            | 1..1                    | One block per stop in the pattern                    | — |

---

## 7.10.4 Rules and cardinalities

| Relationship / Rule                                 | Cardinality | Description |
|------------------------------------------------------|-------------|-------------|
| VehicleJourney → ServiceJourneyPattern               | 1..1        | Each journey SHALL refer to exactly one SJP. |
| VehicleJourney → DayTypeRef                          | 1..*        | At least one calendar MUST be defined. |
| VehicleJourney → passingTimes                        | 1..1        | A complete list of passing times SHALL be provided. |
| TimetabledPassingTime → ArrivalTime                  | 0..1        | Optional at the first stop. |
| TimetabledPassingTime → DepartureTime                | 0..1        | Optional at the final stop. |
| PublicCode                                           | 0..1        | SHOULD be used when a commercial train number exists. |

---

## 7.10.5 XML example

### Example — RE 407 (Luxembourg → Esch-sur-Alzette)

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
