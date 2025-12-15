# 7.13 DayTypeAssignment (ServiceCalendarFrame)

## 7.13.1 Purpose and scope

A **DayTypeAssignment** links a DayType to actual dates or date patterns.  
It defines *when* a DayType is available within an OperatingPeriod, through:

- a weekly pattern (e.g. Monday to Friday),
- a continuous date range,
- individual dates,
- exceptions (inclusion or exclusion).

In the CFL MVP, DayTypeAssignment provides the **operational calendar** used by
VehicleJourneys to determine on which days they run.

---

## 7.13.2 Modelling principles

### Linking abstract and concrete calendars
A DayType is an *abstract category* (“WEEKDAYS”).  
DayTypeAssignment defines *how that category is realised*:

- Monday to Friday during the operating period,
- excluding public holidays,
- including special running days.

### Weekly patterns
The most common case is a weekly repetition (Monday–Friday, Saturday only, etc.).
This is represented using:

```xml
<Properties>
    <DaysOfWeek>MondayToFriday</DaysOfWeek>
</Properties>
```

### Operating period association
A DayTypeAssignment MAY reference an OperatingPeriod to indicate that the rule is  
valid only during a specific timetable period.

### Inclusion / exclusion
- `isAvailable="true"` → the DayType applies on the specified days.  
- `isAvailable="false"` → the DayType does *not* apply (exception).

### MVP simplification
In the CFL MVP:

- Weekly patterns are allowed but optional.  
- Public holiday exceptions are optional.  
- One DayTypeAssignment per DayType is typically sufficient for the MVP.

---
## 7.13.3 Elements and attributes retained in the CFL MVP

| Element / Attribute  | Description | Card. (MVP) | Notes / Constraints | Example value |
|----------------------|-------------|-------------|----------------------|----------------|
| `@id` | Unique identifier of the assignment | 1..1 | Stable CFL identifier | `LU:CFL:DTA:WEEKDAYS` |
| `version` | Object version | 1..1 | Incremented when logic changes | `1` |
| `DayTypeRef` | Reference to the DayType | 1..1 | Mandatory | `LU:CFL:DayType:WEEKDAYS` |
| `OperatingPeriodRef` | Validity period | 0..1 | Optional in MVP | `LU:CFL:OP_2025_WINTER` |
| `Properties` | Weekly pattern definition | 0..1 | Used when pattern repeats weekly | `MondayToFriday` |
| `isAvailable` | Inclusion/exclusion flag | 0..1 | Default = true | `true` |

---

## 7.13.5 XML examples

### Example 1 — WEEKDAYS pattern during winter timetable

```xml
<DayTypeAssignment id="LU:CFL:DTA:WEEKDAYS" version="1" isAvailable="true">
    <DayTypeRef ref="LU:CFL:DayType:WEEKDAYS"/>
    <OperatingPeriodRef ref="LU:CFL:OP_2025_WINTER"/>

    <Properties>
        <DaysOfWeek>MondayToFriday</DaysOfWeek>
    </Properties>
</DayTypeAssignment>
```

### Example 2 - Exception: service does not run on a public holiday

```xml
<DayTypeAssignment id="LU:CFL:DTA:WEEKDAYS_NO_HOLIDAY" version="1" isAvailable="false">
    <DayTypeRef ref="LU:CFL:DayType:WEEKDAYS"/>
    <Date>2025-12-25</Date>
</DayTypeAssignment>
```

