# 7.3 Network entities

# Line (ServiceFrame)

## Purpose and scope

A **Line** represents a public transport axis as perceived by passengers and used to group VehicleJourneys that share the same origin, destination, and general routing.

In the CFL profile, a Line corresponds to a **commercial relation** such as:

- **Luxembourg – Arlon**
- **Luxembourg – Rodange (via Esch-sur-Alzette)**

Each Line defines the commercial and geographical scope of a service family:

- It connects two terminal StopPlaces.
- It may include one or several **via** stations when different variants share the same origin–destination pair.
- Its human-readable name is the axis name, composed of full station names and optional via segments.
- Its identifier is derived from **UIC codes**, ensuring stability, neutrality, and uniqueness.

A Line is **not** a specific train or timetable entry. It is the conceptual corridor on which individual train services (VehicleJourneys) operate.  
Example: the Line *Luxembourg – Arlon* may include multiple VehicleJourneys such as RE 405, RE 407, etc.

CFL structural codes such as **L10, L30, L50, L90** are **not Lines**. They are **GroupOfLines** elements grouping several Lines that share the same network axis or branding (colour, pictogram, marketing identity).

This distinction aligns the Luxembourg NeTEx model with Transmodel principles and keeps consistency between NeTEx (scheduled data) and SIV (passenger-information) layers.

Summary:

- **Line** → commercial/operational axis used by VehicleJourneys.
- **GroupOfLines** → marketing family or network axis (e.g., L10, L30, L50, L90).

Each Line also carries a **ServiceClassification** (e.g., `regionalExpress`, `regional`, `interCity`), allowing downstream systems to filter or group services by type.

---

## Modelling principles

### Nature of Line

A Line represents a **commercial origin–destination relation**.  
It is a passenger-facing axis, not a specific train nor a timetable record.

### Separation of concerns

- Operational objects (`JourneyPattern`, `ServiceJourney`) reference **Line**, not GroupOfLines.
- Marketing families (L10, L30, L50, L90) are represented via **GroupOfLines**.

### Identifier convention (stable & descriptive)

Each Line SHALL have a stable identifier derived from the **UIC codes** of its terminal StopPlaces, optionally extended with via stations when needed to distinguish similar relations.

Pattern:

`id = LU:<OPERATOR>:Line:<UIC_ORIGIN>-<UIC_DESTINATION>[-via-<UIC_INTERMEDIATE(s)>]`


Examples:

- `LU:CFL:Line:8200001-8200027` → Luxembourg – Arlon
- `LU:CFL:Line:8200001-8200095-via-8200060` → Luxembourg – Rodange (via Esch-sur-Alzette)

Rules:

- Identifier uses **only UIC codes**, ensuring technical stability and language neutrality.
- The optional `via` segment is used **only** to distinguish variants sharing the same OD.
- The identifier is **immutable**. Human-readable names remain accented.

### Names (multilingual)

- Format: **“Origin – Destination”** and optional **“(via …)”**.
- **French required**, Luxembourgish and/or German recommended.
- Name uses full, accented station names.

### Accounting and interoperability codes (Debit codes)

Each Line has one or more accounting codes (DebitCodes).  
These are stored in **KeyList**.

Each DebitCode SHALL be represented as:

```xml
<KeyValue typeOfKey="DebitCode" key="…"/>
```

**Current situation**: one DebitCode per line.

**Future-proof**: multiple KeyValue entries.

Example (single DebitCode):

```xml
<Line id="LU:CFL:Line:8200001-8200027" version="1">
  <Name>Luxembourg – Arlon</Name>
  <OperatorRef ref="LU:CFL:Operator:CFL"/>
  <TransportMode>rail</TransportMode>
  <KeyList>
    <KeyValue typeOfKey="DebitCode" key="AVN1000"/>
  </KeyList>
</Line>
```

Example (multiple DebitCodes):

```xml
<Line id="LU:CFL:Line:8200001-8200095-via-8200060" version="1">
  <Name>Luxembourg – Rodange (via Esch-sur-Alzette)</Name>
  <OperatorRef ref="LU:CFL:Operator:CFL"/>
  <TransportMode>rail</TransportMode>
  <KeyList>
    <KeyValue typeOfKey="DebitCode" key="AVN2000"/>
    <KeyValue typeOfKey="DebitCode" key="AVN2001"/>
  </KeyList>
</Line>
```

### Branding and marketing family

- Visual identity (colour/logo) is defined in a **Branding** element.
- A Line MAY reference this visual identity through `BrandingRef`.
- Membership to marketing families (L10, L30, L50, L90) is expressed via `GroupOfLines.members`.

### Routing consistency

All **ServiceJourneyPattern, VehicleJourney, and DatedVehicleJourney** objects related to the same commercial origin–destination SHALL reference the same Line.

---

## Attribute mapping

| Attribute / Element | Definition | Obligation | Notes and LU-specific usage | Example value |
|---------------------|------------|------------|------------------------------|----------------|
| `@id` | Unique identifier | Mandatory | Derived from UIC codes of origin/destination; via segment optional. | `LU:CFL:Line:8200001-8200095-via-8200060` |
| `version` | Object version | Mandatory | Incremented when structural attributes change. | `1` |
| `Name` | Passenger-facing name | Mandatory | Format “Origin – Destination (via …)”; FR mandatory. | Luxembourg – Rodange (via Esch-sur-Alzette) |
| `ShortName` | Abbreviated name | Optional | Used on compact displays. | Lux – Rodange |
| `PrivateCode` | Internal ID | Optional | Unused in initial implementation. | — |
| `KeyList` | Additional keys | Mandatory | Stores DebitCodes and other LU-specific keys. | see examples |
| `ServiceClassification` | Service type | Recommended | CEN vocabulary (`regionalExpress`, `regional`, `interCity`, etc.). | regionalExpress |
| `TransportMode` | Mode | Mandatory | Always `rail` for CFL Lines. | rail |
| `OperatorRef` | Operating company | Mandatory | Always CFL: `LU:CFL:Operator:CFL`. | `LU:CFL:Operator:CFL` |
| `NetworkRef` | Parent network | Recommended | CFL rail network: `LU:CFL:Network:RAIL`. | `LU:CFL:Network:RAIL` |
| `BrandingRef` | Visual identity | Optional | Links to Branding element. | `LU:CFL:Branding:L10` |
| `GroupOfLinesRef` | Marketing membership | Optional | Indicates L10/L30/L50/L90 family membership. | `LU:CFL:GroupOfLines:L10` |

### KeyList conventions (LU)

- **DebitCode** → required  

---

## Rules and cardinalities

| Relationship / Rule | Cardinality | Description and LU-specific constraints |
|---------------------|-------------|----------------------------------------|
| Line → OperatorRef | 1..1 | Must reference exactly one Operator (CFL). |
| Line → GroupOfLinesRef | 0..1 | MAY belong to a marketing family axis. |
| Line → ServiceJourneyPattern / VehicleJourney | 1..* | All related SJPs and VJs SHALL reference the same Line. |

---

### XML example

```xml
<Line id="LU:CFL:Line:8200001-8200095-via-8200060" version="1">
  <Name>Luxembourg – Rodange (via Esch-sur-Alzette)</Name>
  <OperatorRef ref="LU:CFL:Operator:CFL"/>
  <TransportMode>rail</TransportMode>
  <ServiceClassification>regionalExpress</ServiceClassification>

  <KeyList>
    <KeyValue typeOfKey="DebitCode" key="AVN2000"/>
  </KeyList>
</Line>
```

---

# GroupOfLines (ResourceFrame)

## Purpose and scope

A **GroupOfLines** represents a **marketing or network family** that groups several Lines sharing a common axis or visual identity (e.g., L10, L30, L50, L90).

In the Luxembourg profile, a GroupOfLines typically corresponds to a CFL line family as used in public maps and passenger information:

- **L10** – services on the northern axis;
- **L30** – services on the south-west axis;  
- **L50** – services on the west / Arlon axis;
- **L90** – services on the south-east / Thionville axis.

A GroupOfLines is **not** a commercial origin–destination relation and **not** used directly in timetables. It is a **container** for one or more Line objects:

- **Line** → commercial relation (origin–destination, used by journeys);
- **GroupOfLines** → marketing family grouping related Lines (e.g., “L50”).  

Operational objects (`ServiceJourneyPattern`, `VehicleJourney`, `DatedVehicleJourney`) always reference a **Line**, never a GroupOfLines.  
Lines MAY in turn belong to one GroupOfLines.

Visual identity (in particular the **line colour**) is carried at **GroupOfLines** level:

- Each GroupOfLines corresponds to a CFL family (e.g. L50);
- Each family has exactly one line colour;
- Individual Lines inherit this visual identity through their membership to the GroupOfLines.

---

## Modelling principles

### Business role

- GroupOfLines is used to **organise Lines into families** for:
  - Network maps;
  - Passenger-facing information;
  - Reporting or filtering.

- It does **not** change the operational characteristics of the Line.

### Separation from Line

- A **Line** represents a concrete commercial origin–destination relation  
  (e.g., Luxembourg – Arlon, Luxembourg – Rodange via Esch-sur-Alzette).
- A **GroupOfLines** groups these Lines under a shared **CFL marketing code**
  (e.g., L50).

Operational data always reference **Line**, not GroupOfLines.

### Identifier convention

Each GroupOfLines SHALL have a stable identifier derived from the CFL family code.

Pattern:

`LU:CFL:GroupOfLines:<FamilyCode>`

Examples:

- `LU:CFL:GroupOfLines:L10`
- `LU:CFL:GroupOfLines:L30`
- `LU:CFL:GroupOfLines:L50`
- `LU:CFL:GroupOfLines:L90`

Identifiers:

- Are stable and language-neutral;
- Use the CFL codespace and the official family code (Lxx);
- Are not impacted by future renaming of individual Lines.

### Names

- `Name` SHOULD corresponds to a human-readable label (e.g., "Ligne 50").
- French is recommended as primary language; other languages MAY be added in multilingual `<Text>` elements if required.
- A short form (e.g. “L50”) MAY be carried in `ShortName` if used on compact displays.

### Membership semantics

- Each GroupOfLines maintains a `members` list containing one or more `LineRef` elements.
- Each Line MAY reference a GroupOfLines via `GroupOfLinesRef`.
- A Line SHOULD belong to **at most one** GroupOfLines in the CFL profile.

---

## Elements and attributes retained in the CFL MVP

| Element / Attribute      | Description                                      | Cardinality (CFL MVP) | Notes / Constraints                                            | Example value                      |
|--------------------------|--------------------------------------------------|------------------------|----------------------------------------------------------------|------------------------------------|
| `@id`                    | Unique identifier of the GroupOfLines            | 1..1                   | Pattern `LU:CFL:GroupOfLines:<FamilyCode>`.                    | `LU:CFL:GroupOfLines:L50`         |
| `version`                | Object version                                   | 1..1                   | Incremented when the set of member Lines or the name changes. | `1`                                |
| `Name`                   | Human-readable family name                       | 1..1                   | SHOULD include family code and axis name.                      | `L50 – Luxembourg – Arlon`        |
| `ShortName`              | Abbreviated name                                 | 0..1                   | MAY contain only the CFL family code.                          | `L50`                              |
| `Description`            | Textual description of the axis                  | 0..1                   | Optional contextual description.                               | `Luxembourg – Arlon axis`         |
| `BrandingRef`            | Reference to the visual identity (colour)        | 0..1 (SHOULD)          | Points to a Branding element carrying the colour (e.g., #009BD4). | `LU:CFL:Branding:L50`              |
| `members/LineRef`        | References to all Lines belonging to the family  | 1..*                   | MUST list every Line that belongs to this GroupOfLines.        | `LU:CFL:Line:8200001-8200027`     |

### Notes

- **Line colours are in scope for the CFL MVP.**  
  Colours are stored in **Branding/Colour** and referenced from GroupOfLines via `BrandingRef`.
- Individual **Lines do not carry a colour directly**; they inherit the colour of their GroupOfLines.
- No accounting or commercial codes are stored at GroupOfLines level.  
  Such codes remain attached to individual Lines (via Line → KeyList/DebitCode).


---

## Rules and cardinalities

| Relationship / Rule                    | Cardinality | Description and LU-specific constraints                                      |
|----------------------------------------|-------------|-------------------------------------------------------------------------------|
| GroupOfLines → `members/LineRef`       | 1..*        | A GroupOfLines SHALL contain at least one Line.                               |
| Line → `GroupOfLinesRef`               | 0..1        | A Line MAY belong to exactly one GroupOfLines family.                         |
| GroupOfLines → `BrandingRef`           | 0..1 (SHOULD) | A GroupOfLines SHOULD reference one Branding element defining its colour.    |
| GroupOfLines (per family code)         | 0..1        | For each CFL family code (e.g. L50), at most one GroupOfLines SHALL exist.    |

---

## XML example

```xml
<GroupOfLines id="LU:CFL:GroupOfLines:L50" version="1">
  <Name>Ligne 50</Name>
  <ShortName>L50</ShortName>
  <Description>Luxembourg – Arlon axis (CFL marketing family L50).</Description>
  <BrandingRef ref="LU:CFL:Branding:L50"/>

  <members>
    <LineRef ref="LU:CFL:Line:8200001-8200027"/>
    <LineRef ref="LU:CFL:Line:8200027-8200103"/>
  </members>
</GroupOfLines>
```


