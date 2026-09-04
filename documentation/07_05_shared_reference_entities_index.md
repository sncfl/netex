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

Codespaces and frame defaults are declared at CompositeFrame level and apply to the frames contained in the delivery.

Each shared entity used in the CFL MVP is described in a dedicated subsection below and can be referenced from other parts of the documentation.

---

## Structure

1. [Codespace](#codespace)
2. [Operator](#operator)
3. [Shared calendar entities (ServiceCalendarFrame)](#shared-calendar-entities-servicecalendarframe)
   - [DayType (ServiceCalendarFrame)](#daytype-servicecalendarframe)
   - [OperatingPeriod (ServiceCalendarFrame)](#operatingperiod-servicecalendarframe)
   - [DayTypeAssignment (ServiceCalendarFrame)](#daytypeassignment-servicecalendarframe)
4. [TypeOfParking (ResourceFrame)](#typeofparking-resourceframe)
5. [Optional / future shared entities in `resource.xml`](#optional--future-shared-entities-in-resourcexml)
6. [Scope limitations and summary](#scope-limitations-and-summary)

---

## Codespace

### Functional description

A **Codespace** defines a namespace used to qualify identifiers (`id`) in the dataset.  
It ensures global uniqueness and enables unambiguous cross-file referencing.

All identifiers used in the CFL NeTEx MVP dataset rely on Codespaces declared at CompositeFrame level in resource.xml and referenced by the contained frames through FrameDefaults/DefaultCodespaceRef.

```xml
<Codespace id="CFL:Codespace:1">
  <Xmlns>LU:CFL</Xmlns>
  <XmlnsUrl>https://data.cfl.lu/netex/codespace</XmlnsUrl>
  <Description>CFL NeTEx codespace for CFL datasets</Description>
</Codespace>
```

---

### `XmlnsUrl` principle and CFL usage

`XmlnsUrl` is an **identifier of the documentation location** for a Codespace.  
It provides a **canonical reference string** associated with the namespace, but it does **not** guarantee that the URL is publicly reachable, resolvable, or stable for all consumers.

In the CFL NeTEx MVP, the Codespace is declared as:

- `Xmlns = LU:CFL`
- `XmlnsUrl = https://data.cfl.lu/netex/codespace`

This value is used as the **chosen reference URI** for the CFL NeTEx codespace. It may be:
- publicly accessible in the future,
- accessible only within CFL networks,
- or serve only as a stable identifier even if not directly reachable by consumers.

#### How to use it (developer guidance)

- Treat `XmlnsUrl` as **informative metadata / reference URI**: consumers MUST NOT depend on it for processing, validation, or ID resolution.
- The Codespace prefix (`Xmlns`) is what matters for qualifying identifiers used in `@id` and `ref`.
- CFL SHOULD keep `XmlnsUrl` stable over time. If the actual documentation location changes, prefer using redirections or maintaining the reference URI rather than changing the value in published datasets.

#### Usage for objects other than Codespace

`XmlnsUrl` is **specific to Codespace declarations** and SHOULD NOT be replicated at object level (`StopPlace`, `Quay`, `Parking`, `Line`, `VehicleJourney`, etc.).

If object-level links are needed, use the **domain-specific fields** intended for that purpose (examples):
- `BookingUrl` for booking/registration links (e.g. bikebox),
- `ContactDetails/Url` for organisation or support pages.

In short: `XmlnsUrl` documents the **namespace reference** (Codespace), not individual objects, and it is not an availability guarantee.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|----------------------|
| `@id` | Identifier of the Codespace | 1..1 | Primary CFL Codespace identifier: `LU:CFL:Codespace:1`. |
| `Xmlns` | XML namespace URI | 1..1 | Current CFL value: `LU:CFL`. |
| `XmlnsUrl` | Documentation URL | 0..1 | Optional. |
| `Description` | Human-readable description | 0..1 | Informative only. |

---

### CFL-specific modelling rules

- All identifiers used in the dataset **must reference a declared Codespace**.
- Codespaces are stable and must not change across deliveries.
- `XmlnsUrl` SHOULD be provided when a stable documentation URL exists for the codespace.
- The CFL MVP uses the Codespace declared at CompositeFrame level in resource.xml as the primary Codespace.
- Frames contained in the CompositeFrame SHOULD reference the applicable Codespace through FrameDefaults/DefaultCodespaceRef.
- Other LU-level Codespaces may be declared in the same CompositeFrame when needed (e.g., national aggregation or multi-operator contexts).

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

## Shared calendar entities (ServiceCalendarFrame)

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

---

## TypeOfParking (ResourceFrame)

### Functional description

**TypeOfParking** is a shared classification value used to type `Parking` entities published in the CFL NeTEx dataset.

In the CFL profile, `TypeOfParking` is used when a CFL functional parking category cannot be represented adequately by the standard NeTEx parking classifications.

The current CFL implementation defines the following shared value:

* Bikebox secure bicycle parking.

`TypeOfParking` values are defined once in `resource.xml` and referenced from `Parking/TypeOfParkingRef`.

---

### Relationship with `ParkingType`

Standard NeTEx elements and enumerations SHALL be used whenever they adequately represent the required parking semantics.

A CFL-specific `TypeOfParkingRef` SHALL be used only where an additional functional classification is required.

For P+R parkings, the standard NeTEx value `ParkingType = parkAndRide` SHALL be used.

For Bikebox facilities, no standard `ParkingType` value is currently required, because the CFL Bikebox concept is represented through the shared `TypeOfParking` value `BIKEBOX`.

---

### Expected `TypeOfParking` values

The CFL profile currently expects the following shared `TypeOfParking` values:

| TypeOfParking id | Name | Description | Usage |
|------------------|------|-------------|-------|
| `LU:CFL:TypeOfParking:BIKEBOX` | Bikebox | Secure bicycle parking facility. | Used for CFL Bikebox facilities. |

---

### Elements and attributes retained in the CFL profile

| Element / Attribute | Description | Cardinality (CFL profile) | Notes / Constraints |
|---------------------|-------------|----------------------------|---------------------|
| `@id` | Identifier of the `TypeOfParking` value | 1..1 | Stable identifier referenced from `Parking/TypeOfParkingRef`. |
| `version` | Version of the shared value | 1..1 | Current value: `1`. |
| `Name` | Human-readable label of the parking type | 1..1 | Should be short and stable. |
| `Description` | Description of the parking type | 0..1 | Recommended for clarity. |

---

### CFL-specific modelling rules

* `TypeOfParking` values SHALL be defined once in `resource.xml`.
* Bikebox `Parking` entities SHALL reference `LU:CFL:TypeOfParking:BIKEBOX` through `TypeOfParkingRef`.
* Standard NeTEx classifications SHALL be preferred where they adequately represent the required semantics.
* New CFL-specific `TypeOfParking` values SHALL only be introduced where a distinct functional parking category cannot be represented adequately using standard NeTEx concepts.
* `TypeOfParking` identifiers SHALL be stable and SHALL NOT be changed when labels or descriptions are updated.
* Technical characteristics, equipment or real-time capabilities SHALL NOT be modelled as `TypeOfParking` values.

---

### XML example: Bikebox

```xml
<TypeOfParking id="LU:CFL:TypeOfParking:BIKEBOX" version="1">
              <Name>
                <Text lang="en">bikebox</Text>
              </Name>
              <Description>
                <Text lang="en">Secure bike hubs.</Text>
                <Text lang="fr">Parcs sécurisés pour vélos</Text>
                <Text lang="de">Gesicherte Fahrradstationen</Text>
                <Text lang="lb">Sécher Vëlosstatiounen</Text>
              </Description>
</TypeOfParking>
```
---

No additional shared reference entity is introduced at this stage for parking place categories.

Parking place categories SHALL first be represented using standard NeTEx fields and enumerations:
- ParkingUserType / ParkingUserTypes for user-based categories;
- ParkingVehicleType / ParkingVehicleTypes for vehicle-based categories;
- ParkingStayType / ParkingStayList for stay-based categories;
- NumberOfBaysWithRecharging or NumberOfSpacesWithRechargePoint for charging places.

CFL-specific controlled values MAY be introduced later only for categories that cannot be represented clearly using standard NeTEx enumerations.
