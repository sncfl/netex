# 7.15 VehicleJourneyStopAssignment (TimetableFrame)

## 7.15.1 Purpose and scope

A **VehicleJourneyStopAssignment** links a *logical stop* of a VehicleJourney
(ScheduledStopPoint) to a *physical boarding location* (Quay or StopPlace),
for that specific VehicleJourney.

It is used to express:

- the **platform** (Quay) planned for a given stop of a VehicleJourney,
- the **station context** when needed (StopPlaceRef).

In the CFL MVP:

- VehicleJourneyStopAssignment is **optional**.
- It is used when platform information is part of the published timetable or
  needed by downstream systems (e.g. SIV).
- Real-time platform changes are out of scope and handled by SIRI-VM / SIRI-ET.

Assignments are modelled **inside the VehicleJourney**, under `stopAssignments`.

---

## 7.15.2 Modelling principles

### Link between timetable and infrastructure

VehicleJourneyStopAssignment acts as a bridge between:

- the **timetable layer** (VehicleJourney, ScheduledStopPoint),
- the **infrastructure layer** (Quay, StopPlace in the SiteFrame).

### Scope of use in the CFL MVP

- MAY be used when CFL wishes to communicate planned platforms.
- SHOULD be omitted when platforms are considered too volatile or only
  available in real time.

### Reference strategy

In the CFL MVP:

- `ScheduledStopPointRef` is used to identify the logical stop.
- `QuayRef` is used when the planned platform is known.
- `StopPlaceRef` MAY be added when needed, but is not mandatory
  (it can be inferred from the Quay).

Assignments are associated to a VehicleJourney through containment
(`stopAssignments`), not via an explicit VehicleJourneyRef.

---

## 7.15.3 Elements and attributes retained in the CFL MVP

| Element / Attribute      | Description                                   | Card. (MVP) | Notes / Constraints                           | Example value |
|--------------------------|-----------------------------------------------|-------------|-----------------------------------------------|----------------|
| `@id`                    | Identifier of the stop assignment             | 1..1        | Stable within CFL codespace                   | `LU:CFL:VJSA:RE407_LuxGare` |
| `version`                | Object version                                | 1..1        | Incremented when the assignment changes       | `1` |
| `ScheduledStopPointRef`  | Reference to the logical stop                 | 1..1        | MUST reference an existing ScheduledStopPoint | `LU:CFL:ScheduledStopPoint:LuxGare` |
| `QuayRef`                | Reference to the physical platform            | 0..1        | Used when a planned platform is available     | `LU:CFL:Quay:LuxGare-3A` |
| `StopPlaceRef`           | Reference to the station                      | 0..1        | Optional; can often be inferred from Quay     | `LU:CFL:StopPlace:SP00001` |

---

## 7.15.4 Rules and cardinalities

| Rule | Description |
|------|-------------|
| Container | VehicleJourneyStopAssignment elements SHALL appear inside `VehicleJourney/stopAssignments`. |
| One assignment per (VehicleJourney, stop) | At most one assignment SHOULD exist per VehicleJourney and ScheduledStopPoint. |
| ScheduledStopPointRef | SHALL reference an existing ScheduledStopPoint used in the VehicleJourney’s pattern. |
| QuayRef | MAY be present when a planned platform is part of the timetable offer. |
| StopPlaceRef | MAY be omitted when the station can be inferred from the Quay. |

---

## 7.15.5 XML example

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