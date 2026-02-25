# 7.11 DayType (ServiceCalendarFrame)

## 7.11.1 Purpose and scope

A **DayType** represents a reusable *category of operational days* on which one or several VehicleJourneys run.  
It defines **patterns of service availability**, such as:

- Weekdays  
- Weekends  
- Saturday only  
- Public holidays  

In the CFL MVP, DayTypes:

- Provide the **basic calendar structure** used by VehicleJourneys,
- Are referenced through `DayTypeRef`,
- Remain simple and limited to the operational needs of the rail timetable.

A DayType does **not** represent individual dates.  
Concrete date assignment is handled via **DayTypeAssignment**.

---

## 6.11.2 Modelling principles

### Reusability
A DayType is a *named bucket of days* that may be reused by many VehicleJourneys.

### Human-readable naming
Names shall clearly describe the operational meaning (e.g. “Weekdays”).  
Multilingual forms are allowed.

### Stability
DayTypes shall be **stable** and reused across timetable updates when their definition does not change.

### Relationship with VehicleJourney
- A VehicleJourney **must** reference at least one DayType.
- Multiple DayTypes MAY be referenced when needed.

---

## 7.11.3 Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (MVP) | Notes / Constraints | Example value |
|---------------------|-------------|-------------|----------------------|----------------|
| `@id` | Unique identifier of the DayType | 1..1 | Stable CFL identifier | `LU:CFL:DayType:WEEKDAYS` |
| `version` | Object version | 1..1 | Incremented if meaning or name changes | `1` |
| `Name` | Human-readable name | 1..1 | Multilingual `<Text>` allowed | “Weekdays” |
| `ShortName` | Abbreviated form | 0..1 | Optional | “MF” |
| `Properties` | Weekly pattern | 0..1 | Used for patterns such as MondayToFriday | `MondayToFriday` |

---

## 7.11.4 Rules and cardinalities

| Rule | Description |
|------|-------------|
| DayType → Name | SHALL be present at least in French. |
| DayType → Properties | MAY be used when the DayType represents a weekly pattern. |
| Identifiers | SHALL be stable and descriptive (`LU:CFL:DayType:WEEKDAYS`). |
| VehicleJourney → DayTypeRef | A VehicleJourney SHALL reference ≥1 DayType. |

---

## 7.11.5 XML example

```xml
<DayType id="LU:CFL:DayType:WEEKDAYS" version="1">
    <Name>
        <Text xml:lang="fr">Jours ouvrables</Text>
        <Text xml:lang="en">Weekdays</Text>
    </Name>
</DayType>
```