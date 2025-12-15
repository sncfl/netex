# 5. Cross-cutting modelling rules

This chapter defines the transversal rules that apply to the entire dataset, regardless of the frame in which entities appear.  
While Chapter 4 describes *what* is modelled (frames and entities), this chapter explains *how* these elements must be structured, referenced, named and maintained to ensure overall consistency.

The rules below apply to all files and all entities of the MVP.  
They cover identifiers, naming principles, versioning, references between entities, and minimal requirements on temporal consistency and data quality.

## 5.1 Identifier scheme

Identifiers are fundamental in NeTEx: they ensure that entities can be referenced consistently across files, frames and systems.  
The **CFL MVP Profile** defines a clear and uniform identifier structure for all CFL-managed entities, ensuring stability, readability and long-term maintainability.

In the future, when a **LU NeTEx Profile** is established at national level, identifier governance **shall** be extended to all operators and applied consistently across all public-transport data in Luxembourg.  
The rules below therefore distinguish between:

- identifiers defined by **CFL for the MVP**, and  
- identifiers that **will later be governed nationally**.

---

### 5.1.1 Identifier structure

All identifiers follow the general NeTEx pattern:

`<Codespace>:<Class>:<LocalId>`


Examples:  
- `LU:CFL:JourneyPattern:JP01`  
- `LU:CFL:VehicleJourney:VJ48211`  
- `LU:CFL:Line:8200001-8200027`  

This format guarantees global uniqueness and enables consistent cross-referencing throughout the dataset.

---

### 5.1.2 Codespace

The codespace specifies the namespace in which the identifier exists.

- Within the **CFL MVP Profile**, the codespace is: **`LU:CFL`**.  
- When the LU NeTEx Profile will be developed, additional codespaces **shall** be assigned to other operators (e.g. RGTR networks, AVL, TICE, Luxtram) following a coordinated national governance model.

Rules:  
- the codespace always appears first in the identifier;  
- codespaces must be stable and centrally allocated (at national level).


---

### 5.1.3 Class

The second component indicates the NeTEx class of the entity.

Examples:  
`Line`, `JourneyPattern`, `VehicleJourney`, `PassingTime`, `DayType`, `StopPlace`, `Quay`.

Rules:  
- use the **official NeTEx class name**;  
- no abbreviations or operator-specific naming variants.

---

### 5.1.4 LocalId (local identifier component)

The **LocalId** is the third and last part of the identifier.  
It uniquely identifies the entity **within its class and codespace**.  
It is not a separate XML attribute; it is simply the local suffix of the `id`.

Examples:  
- LocalId = `JP01` in `LU:CFL:JourneyPattern:JP01`  
- LocalId = `VJ48211` in `LU:CFL:VehicleJourney:VJ48211`  

#### LocalId formats used in the CFL MVP Profile

For CFL-internal entities involved in the MVP, the following formats are used:

- **Line**: `<UIC-origin>-<UIC-destination>`  
- **JourneyPattern**: `JPnn`  
- **VehicleJourney**: `VJxxxxx`  

These formats ensure stability and predictable referencing within CFL’s own dataset.

#### LocalId for StopPlace and Quay

StopPlace and Quay identifiers **shall not** be defined by the CFL MVP Profile.

Reason:  
Identifiers for physical stops must ultimately come from a **National Stop Registry**, which will be created as part of the future LU NeTEx Profile.  
Until that registry exists, CFL may use temporary internal identifiers, but:

- these are not normative,  
- they will be replaced by national identifiers,  
- the CFL MVP Profile does **not** prescribe any StopPlace or Quay LocalId pattern.

#### What LocalId is *not*

- not a `PrivateCode` (internal operator code),  
- not a `PublicCode` (display code),  
- not a name or label.

Only the complete `id` (Codespace + Class + LocalId) is authoritative for referencing.

---

### 5.1.5 Stability of identifiers

Identifiers defined in the CFL MVP Profile are **permanent**. They do not change when:

- a name changes (e.g. station rename),  
- a pattern is adjusted,  
- operational attributes evolve.

If attributes require modification, a new **version** may be created, but the `id` itself remains unchanged.  
Identifiers must never be reused.

Forbidden constructions:  
- including dates or version numbers;  
- embedding descriptive text;  
- regenerating identifiers between exports.

---

### 5.1.6 Future national governance of identifiers

When the **LU NeTEx Profile** is established, identifier governance **shall** be centralised at national level.  
This governance will include:

- allocation of codespaces for all operators;  
- validation of identifier structure and LocalId formats;  
- enforcement of global uniqueness across the national dataset;  
- a shared model for StopPlace/Quay identifiers;  
- rules for migrating from temporary CFL identifiers to national ones.

Operators **shall** follow this national governance model once in place.

---

### 5.1.7 Future National Stop Registry (NSR)

A **National Stop Registry (NSR)** shall be established as part of the future LU NeTEx Profile.  
Its role will be to provide a **single authoritative source** for the identification of physical stops used across the Luxembourgish public-transport network.

#### Scope of the NSR

The NSR shall define and maintain:

- **StopPlace** entities, as unique representations of physical stations, stops or interchanges;  
- associated topographic, naming and geographic metadata.

StopPlace identifiers used in the CFL MVP Profile are therefore **temporary** and will be replaced by the identifiers allocated by the NSR once it exists.

#### Quay identifiers

The governance of **Quay** identifiers shall be clarified when the LU NeTEx Profile is created.

Two approaches are possible:

1. **National governance of Quays**  
   This is the recommended option for ensuring coherent multimodal integration.  
   Under this model, Quays would be part of the NSR and would have nationally unique identifiers aligned across all operators (CFL, RGTR networks, AVL, TICE, Luxtram, etc.).

2. **Operator-level governance of Quays**  
   If adopted, each operator would maintain its own Quay identifiers while referencing the shared StopPlace.  
   This remains technically feasible but increases the risk of inconsistencies in multimodal passenger information.

Given the high degree of interchange between modes in Luxembourg, a centralised Quay model is **expected** but cannot yet be assumed.

#### Principles once the NSR exists

- each StopPlace shall have **exactly one** national identifier;  
- Quay governance shall follow the national decision (centralised or operator-based);  
- all operators shall reference the same StopPlace identifiers;  
- any update to stop infrastructure (rename, closure, creation of new quays) shall first be applied to the NSR.

Identifiers used during the CFL MVP phase will therefore be **transitional** and replaced once the LU Profile and NSR are formalised.

---

### 5.1.8 Summary

- Identifiers follow the structure `<Codespace>:<Class>:<LocalId>`.  
- CFL defines LocalIds only for **CFL-internal entities**; StopPlace/Quay LocalIds are intentionally **not** defined.  
- Identifiers are stable and must never be reused.  
- A future LU Profile will establish national governance across operators.  
- A National Stop Registry will define authoritative identifiers for StopPlace and Quay.

---

## 5.2 Naming conventions

Naming rules ensure that all public-facing elements (stop names, line names, quay labels) remain clear, consistent and easy for downstream systems to use.  
They distinguish **technical identifiers** (covered in section 5.1) from **public names**, which are intended for passenger information and human readability.

The following conventions apply to all names in the **CFL MVP Profile**.  
In the future LU NeTEx Profile, these naming principles **shall** be harmonised nationally across all operators.

---

### 5.2.1 General principles

- Names must be **human-readable**, **unambiguous**, and **consistent** across the dataset.  
- Public names may include **accents**, **spaces**, **punctuation**, and real-world linguistic forms.  
- Technical constraints apply only to identifiers (`id`), **not** to names.  
- Names must correspond to how locations and services are presented to passengers.

Examples of acceptable names:  
- “Luxembourg Gare Centrale”  
- “Belval-Université”  
- “Wiltz Château”

---

### 5.2.2 StopPlace and Quay names

#### StopPlace.Name

- Must use the official station or locality name used by CFL.  
- Accents, hyphens and spaces are permitted.  
- Names must remain stable unless an official rename occurs.  
- In the future LU Profile, StopPlace names **shall** be aligned nationally through the National Stop Registry.

#### Quay.Name

- Must correspond to the public-facing platform or boarding point (e.g., “Platform 1”, “Platform 3B”).  
- Each Quay name must be **unique within its StopPlace**.  
- The name must reflect the label visible to passengers.  
- In the LU Profile, quay naming conventions **shall** be standardised where relevant to ensure multimodal consistency.

---

### 5.2.3 Line names

Line names describe the public axis of the service.

The CFL MVP Profile uses the pattern:

`<Origin Name> – <Destination Name>`

Examples:  
- “Luxembourg – Arlon”  
- “Luxembourg – Troisvierges – Gouvy”

Rules:  
- Use StopPlace **public names**, not codes.  
- Include “via …” only if used in official communication.  
- Avoid abbreviations unless they are widely recognised.  
- In the future LU Profile, multimodal line naming standards **shall** be defined nationally.

---

### 5.2.4 Languages

The CFL MVP Profile allows names in the following languages when available:

- French (FR)  
- German (DE)  
- English (EN)  
- Luxembourgish (LU) – optional  

Rules:  
- If multiple language variants are provided, their meaning must remain equivalent.  
- CFL may prioritise the language used in its customer information systems.  
- In the LU Profile, language usage **shall** be coordinated across operators to ensure consistency.

---

### 5.2.5 Forbidden constructions

Names must **not** include:

- technical codes or internal references,  
- version numbers or dates,  
- annotations such as “(new)” or “(temporary)”,  
- formatting artefacts (e.g., “_Platform_01_”).

Names must remain clean, readable and aligned with official information for passengers.

---

### 5.2.6 Stability of names

Names may evolve (e.g., following an official rename), but:

- changes must follow CFL or national naming policies,  
- changes should be rare and justified,  
- any update must be applied consistently across all frames referencing the entity.

Changing the public name does **not** change the identifier (see section 5.1).

---
## 5.3 Coding conventions

NeTEx provides optional attributes that allow producers to attach business codes or public-facing codes to entities.  
These codes do not replace the technical identifier (`id`) described in section 5.1.  
Instead, they complement the identifier by providing short labels used internally by operators or externally by passengers.

The **CFL MVP Profile** defines how these codes may be used within CFL data.  
In the future LU NeTEx Profile, the use and governance of such codes **shall** be harmonised across all operators.

---

### 5.3.1 PublicCode

`PublicCode` is an optional attribute intended for **codes visible to passengers** or used widely in communication.

Examples include:

- commercial line numbers (e.g., “10”, “80”, “RE”, “RB”, “IC”),  
- platform identifiers used on signage (when structured codes exist),  
- route designators used on maps or in journey planners.

In the CFL MVP Profile:

- `PublicCode` may be used when CFL communicates a short code publicly.  
- It must remain **short, stable and unambiguous**.  
- It must **not** be used as an identifier and must **not** duplicate the LocalId.

In the future LU Profile, the allocation of `PublicCode` values **shall** be coordinated to avoid collisions between operators, especially for Line numbering.

---

### 5.3.2 PrivateCode

`PrivateCode` is an optional attribute intended for **internal business codes** used by CFL.

Examples include:

- internal stop or quay codes used in legacy CFL systems,  
- internal journey or vehicle pattern codes known by operations,  
- codes required for mapping between CFL systems.

In the CFL MVP Profile:

- `PrivateCode` may be used when needed for internal consistency or traceability.  
- It should not be exposed to passengers.  
- It must remain **operator-specific** and may differ from the conventions used by other operators.

In the future LU Profile, the use of `PrivateCode` shall remain operator-specific unless a national need for harmonisation arises.

---

### 5.3.3 Distinction between identifiers and codes

To avoid ambiguity, the following principles apply:

- `id` (Codespace + Class + LocalId) is the **only authoritative identifier** used for referencing.  
- `PublicCode` and `PrivateCode` **do not** participate in referencing.  
- Names (section 5.2) are intended for human readability, whereas codes support concise labelling.  
- Multiple entities may share the same `PublicCode` if this reflects operational reality (e.g., several VehicleJourneys of the same Line).  
- `PrivateCode` may be duplicated across operators but must remain unique within the scope of the operator using it.

---

### 5.3.4 Stability of codes

Codes should generally remain stable but may evolve:

- if CFL changes its public communication;  
- if internal business processes evolve;  
- if the future LU Profile introduces new national coding practices.

Changing a `PublicCode` or `PrivateCode` **does not** change the identifier and does not require creating a new version of the entity.

---

### 5.3.5 Forbidden constructions for codes

Codes must **not** include:

- full identifiers (“LU:CFL:…”),  
- dates,  
- temporary annotations (“new”, “temp”),  
- long descriptive text,  
- special characters that may conflict with downstream systems.

Codes are meant to remain **short and functional**.

---

## 5.4 Referencing rules

References (`…Ref`) are the mechanism that links the various components of a NeTEx dataset.  
They ensure that entities defined in one frame (e.g., stops, patterns, calendars) can be reused consistently in another (e.g., timetables).  
Correct referencing is essential for producing a coherent and interoperable dataset.

The following rules apply to the **CFL MVP Profile**.  
In the future LU NeTEx Profile, referencing principles **shall** be extended across all operators and aligned with the National Stop Registry.

---

### 5.4.1 General principles

- Every reference (`…Ref`) **must point to an existing, valid `id`**.  
- References must always target the **authoritative frame** for the entity type.  
- A reference is valid only if the referenced entity is present **within the same dataset**.  
- References must be stable and must not depend on names, PublicCodes or PrivateCodes.

Incorrect or unresolved references compromise the integrity of the dataset and prevent reliable downstream consumption.

---

### 5.4.2 Authoritative definition of entities

Each entity must be defined **in exactly one frame**, according to NeTEx best practices.

In the CFL MVP Profile:

- **StopPlace, Quay** → *SiteFrame*  
  *(Temporary CFL identifiers are used until the future National Stop Registry provides authoritative ones.)*

- **Line, JourneyPattern, StopPointInJourneyPattern** → *ServiceFrame*

- **VehicleJourney, PassingTime** → *TimetableFrame*

- **DayType, OperatingDay, OperatingPeriod** → *ServiceCalendarFrame*

- **Codespaces, Operator, Notices** → *ResourceFrame*

Other frames may reference these entities, but none may redefine them.

In the future LU Profile, this structure **shall** be harmonised across all operators.

---

### 5.4.3 Mandatory cross-frame references

Required references include:

- **StopPointInJourneyPattern → QuayRef**  
  Each stop in a pattern must reference a valid Quay of a StopPlace.

- **JourneyPattern → LineRef**  
  Every JourneyPattern must belong to exactly one Line.

- **VehicleJourney → JourneyPatternRef**  
  Timetables must follow the structure defined by the pattern.

- **VehicleJourney → DayTypeRef**  
  Every VehicleJourney must reference at least one operating day.

- **PassingTime → StopPointInJourneyPatternRef**  
  Timing information must align with the sequence defined by the pattern.

These references ensure that all temporal and structural data remain interconnected.

---

### 5.4.4 Rules for consistency

- A `DayTypeRef` must reference a DayType defined in the same dataset.  
- A `QuayRef` must reference a Quay belonging to the correct StopPlace.  
- A `JourneyPatternRef` must reference a JourneyPattern defined in the same ServiceFrame.  
- PassingTimes may only be defined for stops listed in the associated JourneyPattern.  
- No dangling or circular references are allowed.  
- References must remain valid across all dataset deliveries.

In the future LU Profile, these consistency rules **shall** be enforced across all operators sharing stops in the National Stop Registry.

---

### 5.4.5 Forbidden constructions

The following constructions must be avoided:

- referencing entities not included in the dataset being published;  
- referencing identifiers from past or future deliveries;  
- redefining an entity in multiple frames;  
- referencing a StopPlace/Quay outside the scope allowed by the future National Stop Registry;  
- using names or codes (PublicCode, PrivateCode) instead of identifiers.

Such constructions lead to incoherent datasets and must be avoided.

---

### 5.4.6 Summary

Referencing rules ensure that:

- stops defined in the SiteFrame are consistently reused in patterns;  
- JourneyPatterns defined in the ServiceFrame structure the timetable;  
- timetable data aligns with the calendar in the ServiceCalendarFrame;  
- all references remain resolvable and stable across the dataset.

These rules guarantee that the CFL MVP Profile produces a coherent, interoperable dataset.  
In the future LU NeTEx Profile, these referencing principles **shall** support multimodal integration across all operators and rely on the National Stop Registry for authoritative stop structures.


---

## 5.5 Temporal rules

Temporal rules ensure that all scheduled times published in the CFL MVP Profile are consistent, complete and aligned with the structure defined in the ServiceFrame.  
They specify how `PassingTime` elements must be ordered, how arrival and departure times relate to each other, and how VehicleJourneys interact with the operating calendar.

The following rules apply to all timetable data produced by CFL in the MVP.

---

### 5.5.1 Alignment with the JourneyPattern

Each `VehicleJourney` must follow exactly the stop sequence defined in its `JourneyPattern`.

Rules:

- A `VehicleJourney` must include **one `PassingTime` for every `StopPointInJourneyPattern`** of its pattern.  
- Stops may not be omitted or added.  
- Operational flags (e.g., non-boarding or non-alighting) must be expressed using the appropriate NeTEx attributes, not by removing stops.

This ensures that the timetable is structurally consistent with the pattern on which it is based.

---

### 5.5.2 Chronological consistency

Arrival and departure times must form a coherent chronological sequence.

Rules:

- At a given stop:  
  - `ArrivalTime` must be **≤** `DepartureTime`.  
  - One of the two may be omitted when operationally irrelevant (e.g., first or last stops).

- Along the JourneyPattern:  
  - time values must be strictly increasing;  
  - negative running times are not allowed;  
  - scheduled dwell or layover must be reflected in the times.

For overnight journeys, chronology must remain correct using NeTEx constructs such as `DayOffset` where necessary.

---

### 5.5.3 Completeness of timing information

Each stop in a JourneyPattern must contain sufficient temporal data.

Rules:

- A stop that is served for boarding or alighting must include at least one time (`ArrivalTime` or `DepartureTime`).  
- Intermediate operational stops may provide only one of the two.  
- A stop must not be left completely without time information unless explicitly marked as non-serviceable.

Timetable data must therefore be complete and represent operational reality.

---

### 5.5.4 Association with DayTypes

Every `VehicleJourney` must reference at least one DayType.

Rules:

- `DayTypeRef` must point to a DayType defined in the same dataset.  
- Multiple DayTypes may be assigned when needed.  
- DayTypes must accurately reflect the real operating days of the service.

A `VehicleJourney` without a `DayTypeRef` is not permitted.

---

### 5.5.5 Forbidden temporal constructions

To maintain consistency and data quality, the following must not occur:

- a served stop with no `ArrivalTime` and no `DepartureTime`;  
- `PassingTime` elements out of sequence with the JourneyPattern;  
- artificial placeholder times (e.g., “00:00” for unknown times);  
- `VehicleJourney` elements without any `DayTypeRef`;  
- encoding operational variants within identifiers.

Such cases result in ambiguous or invalid timetable data.

---

### 5.5.6 Summary

The temporal rules ensure that all CFL timetable data in the MVP:

- follows the structure of the JourneyPattern,  
- contains coherent and complete timing information,  
- is chronologically valid,  
- and is correctly associated with operating days.

These rules guarantee that the published timetable is reliable, interpretable and suitable for downstream systems.
