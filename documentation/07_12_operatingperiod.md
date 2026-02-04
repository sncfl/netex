# 7.12 OperatingPeriod (ServiceCalendarFrame)

## 7.12.1 Purpose and scope

An **OperatingPeriod** defines a *continuous date range* during which a timetable or a set of VehicleJourneys is valid.

It provides the temporal boundaries of the timetable dataset, typically aligned with seasonal timetable changes (e.g. winter period, summer period).

An OperatingPeriod does **not** specify which days within the period are operational.  
It only defines the *outer validity range*.  
Actual running days are determined by **DayType** and **DayTypeAssignment**.

In the CFL MVP:

- At least one OperatingPeriod SHALL be defined per dataset.
- A VehicleJourney MAY (optionally) reference an OperatingPeriod through calendar assignments.

---

## 7.12.2 Modelling principles

### Continuous range  
An OperatingPeriod represents an uninterrupted period starting on `FromDate` and ending on `ToDate` (inclusive).

### Stability  
OperatingPeriods remain stable across timetable updates as long as the date range does not change.

### Independence from DayType  
OperatingPeriods do not imply any pattern (e.g. weekdays, weekends).  
They only define *when the timetable is active* at a high level.

---

## 7.12.3 Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (MVP) | Notes / Constraints | Example value |
|---------------------|-------------|-------------|----------------------|----------------|
| `@id` | Unique identifier | 1..1 | Stable identifier for the period | `LU:CFL:OP_2025_WINTER` |
| `version` | Object version | 1..1 | Incremented if dates change | `1` |
| `FromDate` | Start date (YYYY-MM-DD) | 1..1 | Inclusive | `2025-12-15` |
| `ToDate` | End date (YYYY-MM-DD) | 1..1 | Inclusive | `2026-06-14` |

---

## 7.12.4 Rules and cardinalities

| Rule | Description |
|------|-------------|
| OperatingPeriod → FromDate | SHALL be present. |
| OperatingPeriod → ToDate | SHALL be present. |
| Identifier | SHALL be stable and descriptive. |
| Usage | At least one OperatingPeriod SHALL exist in the dataset. |

---

## 7.12.5 XML example

```xml
<OperatingPeriod id="LU:CFL:OP_2025_WINTER" version="1">
    <FromDate>2025-12-15</FromDate>
    <ToDate>2026-06-14</ToDate>
</OperatingPeriod>
