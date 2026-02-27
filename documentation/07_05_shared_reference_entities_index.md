# 7.5 Shared reference entities (resource.xml)

## Overview

The `resource.xml` file contains **shared reference entities reused across the CFL NeTEx MVP bundle**.

These entities are grouped here because they:
- are transversal by nature;
- are not tied to a specific commercial line (`line_<LineId>.xml`);
- and are published together in `resource.xml` under shared frames.

In the CFL MVP, `resource.xml` may include:
- a **ResourceFrame** (organisations, reusable reference objects, etc.),
- a **ServiceCalendarFrame** (calendar primitives reused by multiple line files),
- and, when needed, a **ServiceFrame** for shared transfer connections.

Each shared entity used in the CFL MVP is described in a dedicated subsection below and can be referenced from other parts of the documentation.

---

## Codespace

### Functional description

A **Codespace** defines a namespace used to qualify identifiers (`id`) in the dataset.  
It ensures global uniqueness and enables unambiguous cross-file referencing.

All identifiers used in the CFL NeTEx MVP dataset rely on Codespaces declared in the **ResourceFrame of `resource.xml`**.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|----------------------|
| `@id` | Identifier of the Codespace | 1..1 | Stable identifier defined by CFL or national governance. |
| `xmlns` | XML namespace URI | 1..1 | Must be unique. |
| `xmlnsUrl` | Documentation URL | 0..1 | Optional. |
| `Description` | Human-readable description | 0..1 | Informative only. |

---

### CFL-specific modelling rules

- All identifiers used in the dataset **must reference a declared Codespace**.
- Codespaces are stable and must not change across deliveries.
- The CFL MVP uses `LU:CFL` as its primary codespace.
- Other LU-level codespaces may be declared in the same ResourceFrame when needed (e.g., national aggregation or multi-operator contexts).

---

## Operator

### Functional description

The **Operator** represents the organisation responsible for operating the services described in the dataset.

In the CFL NeTEx MVP, a single Operator is defined and reused across all Lines and services.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|----------------------|
| `@id` | Identifier of the Operator | 1..1 | Must follow the CFL identifier scheme. |
| `Name` | Official operator name | 1..1 | Human-readable name. |
| `ShortName` | Short label | 0..1 | Optional, if used consistently. |
| `ContactDetails` | Contact information | 0..1 | Optional, minimal use in MVP. |

---

### CFL-specific modelling rules

- The CFL MVP defines **exactly one Operator** for the rail MVP dataset.
- All Line and service entities reference this Operator using `OperatorRef`.
- Additional Operators (e.g., CFL bus, CFL Flex) may be introduced in later extensions or in multi-operator publications.
- Operator identifiers are stable and never reused.

---

## Branding

### Functional description

**Branding** defines visual or commercial branding information that may be associated with services or Lines.

It enables downstream systems to apply a consistent visual identity (e.g., colours, logos) when presenting services to passengers.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|----------------------|
| `@id` | Identifier of the Branding | 1..1 | Stable identifier. |
| `Name` | Branding name | 1..1 | Human-readable label. |
| `Description` | Description of branding | 0..1 | Optional. |

---

### CFL-specific modelling rules

- Branding entities are defined **once** in the ResourceFrame.
- Branding may be referenced from Line or service entities if required.
- Branding usage remains optional in the MVP and may be expanded later.

---

## Shared calendar entities (ServiceCalendarFrame)

The **ServiceCalendarFrame** in `resource.xml` contains calendar primitives that are **reused by multiple line files**.  
Line timetables reference these objects (e.g., via `DayTypeRef`) and must not redefine them locally.

The CFL MVP documents here the baseline calendar building blocks:
- `DayType`
- `OperatingPeriod`
- `DayTypeAssignment`

---

## DayType (ServiceCalendarFrame)

### Purpose and scope

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

### Modelling principles

#### Reusability
A DayType is a *named bucket of days* that may be reused by many VehicleJourneys.

#### Human-readable naming
Names shall clearly describe the operational meaning (e.g. “Weekdays”).  
Multilingual forms are allowed.

#### Stability
DayTypes shall be **stable** and reused across timetable updates when their definition does not change.

#### Relationship with VehicleJourney
- A VehicleJourney **must** reference at least one DayType.
- Multiple DayTypes MAY be referenced when needed.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (MVP) | Notes / Constraints | Example value |
|---------------------|-------------|-------------------|----------------------|--------------|
| `@id` | Unique identifier of the DayType | 1..1 | Stable CFL identifier | `LU:CFL:DayType:WEEKDAYS` |
| `version` | Object version | 1..1 | Incremented if meaning or name changes | `1` |
| `Name` | Human-readable name | 1..1 | Multilingual `<Text>` allowed | “Weekdays” |
| `ShortName` | Abbreviated form | 0..1 | Optional | “MF” |
| `Properties` | Weekly pattern | 0..1 | Used when the DayType represents a weekly pattern (e.g. MondayToFriday). | `MondayToFriday` |

---

### Rules and cardinalities

| Rule | Description |
|------|-------------|
| DayType → Name | SHALL be present at least in French. |
| DayType → Properties | MAY be used when the DayType represents a weekly pattern. |
| Identifiers | SHALL be stable and descriptive (`LU:CFL:DayType:WEEKDAYS`). |
| VehicleJourney → DayTypeRef | A VehicleJourney SHALL reference ≥1 DayType. |

---

### XML example

```xml
<DayType id="LU:CFL:DayType:WEEKDAYS" version="1">
    <Name>
        <Text xml:lang="fr">Jours ouvrables</Text>
        <Text xml:lang="en">Weekdays</Text>
    </Name>
</DayType>
```

---

## OperatingPeriod (ServiceCalendarFrame)

### Purpose and scope

An **OperatingPeriod** defines a *continuous date range* during which a timetable or a set of VehicleJourneys is valid.

It provides the temporal boundaries of the timetable dataset, typically aligned with seasonal timetable changes (e.g. winter period, summer period).

An OperatingPeriod does **not** specify which days within the period are operational.  
It only defines the *outer validity range*.  
Actual running days are determined by **DayType** and **DayTypeAssignment**.

In the CFL MVP:

- At least one OperatingPeriod SHALL be defined per dataset.
- A VehicleJourney MAY (optionally) reference an OperatingPeriod through calendar assignments.

---

### Modelling principles

#### Continuous range

An OperatingPeriod represents an uninterrupted period starting on `FromDate` and ending on `ToDate` (inclusive).

#### Stability

OperatingPeriods remain stable across timetable updates as long as the date range does not change.

#### Independence from DayType

OperatingPeriods do not imply any pattern (e.g. weekdays, weekends).  
They only define *when the timetable is active* at a high level.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (MVP) | Notes / Constraints | Example value |
|---------------------|-------------|-------------------|----------------------|--------------|
| `@id` | Unique identifier | 1..1 | Stable identifier for the period | `LU:CFL:OP_2025_WINTER` |
| `version` | Object version | 1..1 | Incremented if dates change | `1` |
| `FromDate` | Start date (YYYY-MM-DD) | 1..1 | Inclusive | `2025-12-15` |
| `ToDate` | End date (YYYY-MM-DD) | 1..1 | Inclusive | `2026-06-14` |

---

### Rules and cardinalities

| Rule | Description |
|------|-------------|
| OperatingPeriod → FromDate | SHALL be present. |
| OperatingPeriod → ToDate | SHALL be present. |
| Identifier | SHALL be stable and descriptive. |
| Usage | At least one OperatingPeriod SHALL exist in the dataset. |

---

### XML example

```xml
<OperatingPeriod id="LU:CFL:OP_2025_WINTER" version="1">
    <FromDate>2025-12-15</FromDate>
    <ToDate>2026-06-14</ToDate>
</OperatingPeriod>
```

---

## DayTypeAssignment (ServiceCalendarFrame)

### Purpose and scope

A **DayTypeAssignment** links a DayType to actual dates or date patterns.  
It defines *when* a DayType is available within an OperatingPeriod, through:

- A weekly pattern (e.g. Monday to Friday),
- A continuous date range,
- Individual dates,
- Exceptions (inclusion or exclusion).

In the CFL MVP, DayTypeAssignment provides the **operational calendar** used by VehicleJourneys to determine on which days they run.

---

### Modelling principles

#### Linking abstract and concrete calendars

A DayType is an *abstract category* (“WEEKDAYS”).  
DayTypeAssignment defines *how that category is realised*:

- Monday to Friday during the operating period,
- Excluding public holidays,
- Including special running days.

#### Weekly patterns

The most common case is a weekly repetition (Monday–Friday, Saturday only, etc.).  
This is represented using a weekly pattern structure under `Properties` (exact XML structure depends on the NeTEx XSD used).

#### Operating period association

A DayTypeAssignment MAY reference an OperatingPeriod to indicate that the rule is valid only during a specific timetable period.

#### Inclusion / exclusion

- `isAvailable="true"` → the DayType applies on the specified days.  
- `isAvailable="false"` → the DayType does *not* apply (exception).

#### MVP simplification

In the CFL MVP:

- Weekly patterns are allowed but optional.  
- Public holiday exceptions are optional.  
- One DayTypeAssignment per DayType is typically sufficient for the MVP.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Card. (MVP) | Notes / Constraints | Example value |
|---------------------|-------------|-------------|----------------------|--------------|
| `@id` | Unique identifier of the assignment | 1..1 | Stable CFL identifier | `LU:CFL:DTA:WEEKDAYS` |
| `version` | Object version | 1..1 | Incremented when logic changes | `1` |
| `DayTypeRef` | Reference to the DayType | 1..1 | Mandatory | `LU:CFL:DayType:WEEKDAYS` |
| `OperatingPeriodRef` | Validity period | 0..1 | Optional in MVP | `LU:CFL:OP_2025_WINTER` |
| `Properties` | Weekly pattern definition | 0..1 | Used when pattern repeats weekly | `MondayToFriday` |
| `isAvailable` | Inclusion/exclusion flag | 0..1 | Default = true | `true` |

---

### XML examples

#### Example 1 — WEEKDAYS pattern during winter timetable

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

---

## Optional / future shared entities in `resource.xml`

The following shared entities are **not part of the CFL MVP baseline**, but may be introduced in future extensions of the profile.

They are listed here to document their **expected location and role** when introduced.

---

### Notice

**Notice** is used to provide shared textual information (e.g., warnings, informational messages, detailed information on accessibility) that may be referenced by multiple entities across Frames.

---

### ValueSet and TypeOfValue

**ValueSet** and **TypeOfValue** are used to define controlled vocabularies and categorical value lists shared across the dataset.

They may be introduced when formal value governance is required.

---

### Interchange and transfer-related entities

If shared interchange or transfer information is published, the corresponding entities shall be defined in a **ServiceFrame within `resource.xml`**, as recommended by NeTEx experts.

These entities are not included in the CFL MVP baseline.

---

## Scope limitations and summary

The shared frames published in `resource.xml` in the CFL NeTEx MVP:

- Do not model networks or organisational hierarchies beyond the Operator;
- Do not include fare-related reference data;
- Do not include accessibility metadata (covered by later work packages);
- Do not include real-time or operational reference entities.

`resource.xml` provides the **shared foundation** of the CFL NeTEx MVP dataset by ensuring:

- Consistent identifier namespaces;
- A single authoritative Operator definition;
- Reusable branding information;
- And shared calendar primitives reused across line files.

All other frames rely on shared entities defined in `resource.xml` through stable references.
