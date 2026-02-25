# 7.5 GroupOfLines (ResourceFrame)

## 7.5.1 Purpose and scope

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

## 7.5.2 Modelling principles

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

## 7.5.3 Elements and attributes retained in the CFL MVP

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

## 7.5.4 Rules and cardinalities

| Relationship / Rule                    | Cardinality | Description and LU-specific constraints                                      |
|----------------------------------------|-------------|-------------------------------------------------------------------------------|
| GroupOfLines → `members/LineRef`       | 1..*        | A GroupOfLines SHALL contain at least one Line.                               |
| Line → `GroupOfLinesRef`               | 0..1        | A Line MAY belong to exactly one GroupOfLines family.                         |
| GroupOfLines → `BrandingRef`           | 0..1 (SHOULD) | A GroupOfLines SHOULD reference one Branding element defining its colour.    |
| GroupOfLines (per family code)         | 0..1        | For each CFL family code (e.g. L50), at most one GroupOfLines SHALL exist.    |

---

## 7.5.5 XML example

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
