# 7.14 TimetabledPassingTime (TimetableFrame)

## 7.14.1 Purpose and scope

A **TimetabledPassingTime** provides the *scheduled theoretical time* at which a
VehicleJourney is planned to arrive at and/or depart from a specific stop  
(as defined in the referenced StopPointInJourneyPattern).

It contains:

- the scheduled **ArrivalTime**,
- the scheduled **DepartureTime**,
- and the reference to the stop in the journey pattern.

In the CFL MVP:

- Every VehicleJourney SHALL contain one TimetabledPassingTime per stop.
- Only theoretical times are included; real-time updates belong to SIRI-ET/VM.

---

## 7.14.2 Modelling principles

### One passing time per stop
For each StopPointInJourneyPattern in the ServiceJourneyPattern, the corresponding
VehicleJourney SHALL include exactly one TimetabledPassingTime.

### Arrival vs departure times
- At the **origin stop**: only `DepartureTime` is normally present.  
- At the **terminus**: only `ArrivalTime` is normally present.  
- At **intermediate stops**: both MAY be present.

### Theoretical data only
TimetabledPassingTime represents *planned schedule*, not real-time updates.

### No platform assignment inside this element
Platform (Quay) assignment is modelled via **VehicleJourneyStopAssignment**, not here.

---

## 7.14.3 Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Card. (MVP) | Notes / Constraints | Example |
|---------------------|-------------|--------------|----------------------|---------|
| `StopPointInJourneyPatternRef` | Reference to the stop in the pattern | 1..1 | Mandatory | `LU:CFL:SPJP:LuxGare_1` |
| `ArrivalTime` | Scheduled arrival time | 0..1 | Optional at first stop | `08:23:00` |
| `DepartureTime` | Scheduled departure time | 0..1 | Optional at last stop | `08:24:00` |

---

## 7.14.4 Rules and cardinalities

| Rule | Description |
|------|-------------|
| One TimetabledPassingTime per stop | The number of passingTimes MUST match the number of stops in the pattern. |
| Arrival/Departure optionality | Origin: no ArrivalTime. Terminus: no DepartureTime. |
| Time format | SHALL use `HH:MM:SS` (ISO 8601). |
| No platform assignment | QuayRef SHALL NOT appear here. |

---

## 7.14.5 XML example

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