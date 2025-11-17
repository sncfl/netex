# 3. Organization of Data

## 3.1 PublicationDelivery (the exchange envelope)

Each XML file is a NeTEx **PublicationDelivery**: an envelope containing one `CompositeFrame` and essential metadata (issuer, timestamp, description).

For WP1, the CFL profile applies the following rules:

- **One PublicationDelivery per XML file.**
- **One CompositeFrame per XML file.**
- **The CompositeFrame may contain one or several business frames, depending on the role of the file:**
  - `resource.xml` contains several frames (a `ResourceFrame`, a `ServiceFrame` for transfer connections, and a `ServiceCalendarFrame`).
  - `stop.xml` contains a single `SiteFrame`.
  - Each line-specific file contains one `ServiceFrame` and one `TimetableFrame`.

- **Mandatory metadata** include:
  - `PublicationTimestamp`
  - `ParticipantRef`
  - a human-readable `Description`

This approach keeps each file small, focused, and easy to validate.

Although NeTEx allows combining multiple business frames inside a single `CompositeFrame`, the Luxembourg profile applies a **constrained, documented structure per file type** to simplify validation, maintenance and overall understanding.  
The only exception concerns timetables: **several TimetableFrames may be grouped in the same file**, but only when they all refer to the same `Line`.

## 3.2 CompositeFrames used in WP1

WP1 relies on a limited subset of NeTEx frames.  
Only the frames required for describing stops, service structures, calendars and timetables are included.

| Frame                       | Scope / Content                                   | Key Entities                                           |
|-----------------------------|---------------------------------------------------|---------------------------------------------------------|
| **ResourceFrame**           | Shared reference data reused across all files     | Codespace, Organisation, Operator, Authority, TypeOfValue, Notice |
| **ServiceFrame (connections)** | Physical transfer connections between StopPlaces | Connection, Transfer, TransferDuration |
| **ServiceCalendarFrame**    | Shared operating days and periods                 | DayType, OperatingPeriod, OperatingDay, DayTypeAssignment |
| **SiteFrame**               | Stop referential                                  | StopPlace, Quay |
| **ServiceFrame (per line)** | Line-specific service structure                   | Line, JourneyPattern, StopPointInJourneyPattern |
| **TimetableFrame (per line)** | Line-specific timetable                         | VehicleJourney, PassingTime |

Each frame is self-contained and references other frames through **stable identifiers**, ensuring consistency across the dataset.

## 3.3 File publication strategy (CFL scheduled dataset)

This section describes how XML files are structured and named within WP1.

The CFL profile follows a multi-file strategy to ensure modularity, ease of validation, and predictable consumption by downstream systems:

- All shared reference data are centralised in one file.
- All stops and quays are centralised in a dedicated file.
- Each commercial line has its own line-specific file grouping both the service structure and the timetable.

---

### 3.3.1 File structure

| File name                | Frame(s) included                                                                                   | Key content (entities)                                                                      | Role                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| **stop.xml**             | SiteFrame                                                                                            | StopPlace, Quay                                                                                | Authoritative repository of all stops and quays.                                       |
| **line_<LineId>.xml**    | ServiceFrame<br>TimetableFrame                                                                       | Line definition, JourneyPatterns, StopPointInJourneyPattern<br>, VehicleJourney, PassingTime | Complete service and timetable for a single commercial line.                          |
| **resource.xml**         | ResourceFrame<br>ServiceFrame (connections)<br>ServiceCalendarFrame                                   | Codespaces, Notices, shared ValueSets/Enumerations, Organisations, Operators, Calendars, Transfer connections | Central shared referential for the entire dataset.                                     |
| **network.xml** *(optional)* | ServiceFrame (marketing- or network-level structures)                                             | Branding, GroupOfLines, possible network definitions                                           | Reserved for future use. Not delivered in WP1.                                         |

---

### 3.3.2 Naming rules

**Shared files** use fixed names:

- `resource.xml`  
- `stop.xml`

**Line-specific files** follow the pattern:

```text
line_<LineIdentifier>.xml
```

Where `<LineIdentifier>` is derived from the Line id (e.g. the suffix of `LU:CFL:Line:8200001-8200027`).

**Examples:**

- `line_8200001-8200027.xml`
- `line_8200001-8200095-via-8200060.xml`

Several TimetableFrames for the same Line may be grouped into one file.

Auxiliary files for future work packages (e.g., accessibility, fares) may also be added later.

#### Multi-operator notes

- In a national aggregated publication, **XML filenames MUST remain operator-neutral**; operator attribution is expressed in the data (OperatorRef, AuthorityRef, etc.).
- If multiple operators publish independently, ZIP archive names **may include the operator**, but XML filenames should remain neutral.
- All operator codespaces (e.g. `LU:CFL`, `LU:RGTR`, `LU:TICE`) **must be declared in `resource.xml`** and used consistently.

---

### 3.3.3 Cross-file references

The dataset follows a strict **“single authority per entity”** principle.

- `stop.xml` is the authoritative source for **StopPlace** and **Quay**.
- `resource.xml` is authoritative for **Organisations**, **Operators**, **Codespaces**, **Notices**, **Calendars**, and **Transfer connections**.
- `line_<LineId>.xml` files contain the authoritative definitions of **Line**, **JourneyPattern**, **VehicleJourney**, and **PassingTime**.

Other files reference these entities using stable identifiers.

**Examples:**

- A `JourneyPattern` in a line file references a `Quay` defined in `stop.xml`.
- A `VehicleJourney` references a `DayType` defined in `resource.xml`.

Identifiers are globally unique and always include a declared codespace:

- `LU:CFL:StopPlace:SP00045`
- `LU:CFL:Quay:BASCHARAGE-SANEM-1`
- `LU:CFL:Line:8200001-8200027`
- `LU:CFL:VehicleJourney:V12345`

Codespaces are declared in the ResourceFrame of `resource.xml`.

---

### 3.3.4 Separation of concerns

Each file has a clearly defined role:

#### **resource.xml**
- Codespaces, organisations, operators  
- Notices and value sets  
- Calendars (DayTypes, OperatingPeriods, DayTypeAssignments)  
- Transfer connections  

#### **stop.xml**
- All StopPlaces and Quays  
- Coordinates and identifiers  

#### **line_<LineId>.xml**
- Line definition  
- JourneyPatterns  
- Logical-to-physical stop associations (ScheduledStopPoint → QuayRef)  
- VehicleJourney definitions  
- PassingTimes (timetable)  

This structure ensures that timetable data can always be resolved against consistent references, avoiding duplication, ambiguity, or conflicts.

## 3.4 Delivery bundles

This section defines how XML files are grouped into delivery bundles.  
Not every delivery needs to include all XML files.  
Depending on the use case, CFL will publish different delivery bundles, each packaged as a ZIP archive (see §3.5).

| Bundle type            | Content                                                | When                                                         |
|------------------------|---------------------------------------------------------|--------------------------------------------------------------|
| **Baseline delivery**  | `resource.xml`, `stop.xml`, all `line_<LineId>.xml`     | Initial deployment, yearly resets, major structural changes. |
| **Reference delivery** | `resource.xml`, `stop.xml`                              | Updates to reference data with no timetable changes.         |
| **Timetable update**   | One or more `line_<LineId>.xml`                         | Time-related changes for one or several lines.               |
| **Calendar update**    | `resource.xml` (ServiceCalendarFrame) + affected line files | Updates of operating periods or public holidays.         |
| **New stop / new line**| Updated `stop.xml` and/or `resource.xml` + affected line files | Ensures resolvability of new identifiers.              |
| **Mode-specific delivery** | All updated line files for one mode (e.g. Train)     | Coordinated timetable change for an entire mode.             |

---

## 3.5 Archiving and versioning

Deliveries constitute official snapshots of the CFL dataset. Strict rules ensure reproducibility and trust.

---

### 3.5.1 Archiving rules

- Each delivery is a single ZIP archive.
- XML filenames inside the ZIP follow stable naming conventions.
- ZIP names include the bundle type and a UTC timestamp:

```text
LU_NeTEx_<BundleType>_<YYYYMMDDThhmmZ>.zip
```

**Examples:**

- `LU_NeTEx_BaselineDelivery_20250922T0600Z.zip`
- `LU_NeTEx_ReferenceDelivery_20250922T0600Z.zip`
- `LU_NeTEx_TimetableUpdate_20250922T0600Z.zip`

**Immutability:**  
Once published, a delivery is final. Corrections are issued as a new delivery.

**Retention:**  
All deliveries are archived permanently.

---

### 3.5.2 Entity versioning (inside frames)

- Each entity carries a `version` attribute.
- A new version is created whenever an entity changes.
- Temporal validity is tracked via `ValidFrom` / `ValidTo` or `ValidBetween`.
- Identifiers are stable and never reused.
- Additive change is preferred over deletion: retired entities remain in the dataset with an end of validity.

**Example:**  
StopPlace Luxembourg  
- v1 (2018–2025)  
- v2 (from 2025)

---

### 3.5.3 Profile versioning (Luxembourg NeTEx Profile)

Beyond entity-level versioning, the Luxembourg NeTEx profile itself evolves.  
A semantic versioning scheme communicates the type of changes and their impact.

**Version number format:**  

```text
<MAJOR>.<MINOR>.<PATCH>
```


- **MAJOR — Incompatible changes**  
  Breaking changes requiring consumers to adapt.  
  Example: `v1.4.2 → v2.0.0` if the identifier scheme changes.

- **MINOR — Backward-compatible additions**  
  New optional elements or attributes, with continued compatibility.  
  Example: `v1.2.3 → v1.3.0` if an optional `AccessibilityFeature` is added.

- **PATCH — Non-breaking corrections**  
  Documentation fixes, corrected enumerations, etc.  
  Example: `v1.2.3 → v1.2.4` for a ValueSet typo fix.

**Governance rules:**

- Every official profile release MUST have a documented version number.
- MAJOR and MINOR increments MUST be communicated before deployment.
- Detailed governance rules are described in Chapter 6.

---

### 3.5.4 Compatibility principles

Compatibility ensures that each new delivery integrates smoothly with consumer systems and that datasets remain interpretable across time.

#### Backward compatibility
Backward compatibility is prioritised.

#### Resolvable references

A delivery MUST NOT contain references to entities absent from the current or previous baseline/reference deliveries.

Examples:
- If a new StopPlace appears in `stop.xml` and a timetable references it, **both must be delivered together**.
- A timetable MUST NOT reference a StopPlace that consumers cannot resolve.

#### Single authority per entity type

Each type of entity has exactly one authoritative source file:

- StopPlace, Quay → `stop.xml`
- Line, JourneyPattern → `line_<LineId>.xml`
- DayType → `resource.xml`
- VehicleJourney → `line_<LineId>.xml`

Examples:
- Stops must not be redefined in timetables.
- Calendars must not be duplicated in line-specific files.

#### Consistency of references

Identifiers MUST remain stable across deliveries.

Examples:
- `LU:CFL:StopPlace:Luxembourg` must appear identically in all future deliveries.
- If the stop is renamed (“Luxembourg Gare Centrale” → “Luxembourg Central”), the identifier stays the same; only the Name changes.

#### Integrity of bundles

Each bundle type ensures internal consistency.

Examples:
- A Calendar update includes both the updated `resource.xml` calendar elements and the affected line files.
- A New stop / new line delivery pairs the updated reference file(s) with the corresponding timetable(s).

