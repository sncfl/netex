# 6. Overview of XML Publications and Contained Entities

This chapter provides a **synthetic overview** of the XML files produced in the CFL NeTEx MVP
and indicates **where each Frame and entity is physically published**.

It is intended as a **navigation aid** for readers, before consulting:
- the conceptual description of Frames
  ([4. Frames description](04_frames_description.md)),
- and the detailed NeTEx entity specifications
  ([7. How to read entity specifications](07_01_how_to-read_entity_specs.md)).

This chapter does **not** define new modelling rules.
The authoritative rules for file organisation, publication structure,
and cross-file references are defined in **Chapter 3**.


---

## 6.1 Publication files and content overview

The CFL NeTEx MVP dataset is published as a set of XML files.
Each file contains one or more NeTEx Frames encapsulated in a `PublicationDelivery`.

The table below summarises, for each XML file:
- the Frames it contains,
- the NeTEx entities defined in that file.


---

### Table — XML files, frames and entities

| XML file | Frames included | Entities defined in the file |
|----------|-----------------|------------------------------|
| `resource.xml` | `ResourceFrame` | Codespace, Operator, Notice, ValueSet |
| | `ServiceCalendarFrame` | DayType, OperatingPeriod, OperatingDay, DayTypeAssignment |
| `stop.xml` | `SiteFrame` | StopPlace, Quay |
| `line_<LineId>.xml` | `ServiceFrame` | Line, JourneyPattern, StopPointInJourneyPattern |
| | `TimetableFrame` | VehicleJourney, PassingTime |


---

## 6.2 Reading this overview

This overview table is the entry point for navigating the rest of the documentation:

- it shows **where each entity is published** in the XML dataset,
- it allows producers and consumers to quickly locate entities across files,
- it provides the concrete link between:
  - the conceptual Frames described in
    [Frames description](04_frames_description.md), and
  - the detailed NeTEx entity specifications described in
    [How to read entity specifications](07_01_how_to-read_entity_specs.md).

The detailed modelling rules, constraints and examples for each entity
are provided exclusively in **Chapter 7**.

---

## 6.3 Relationship with data organisation rules

The file structure and publication logic summarised in this chapter
follow the data organisation principles defined in **Chapter 3**.

In particular:
- each entity type is defined in exactly one XML file,
- other files may reference these entities but must not redefine them,
- cross-file references rely exclusively on NeTEx reference mechanisms (`…Ref`).

This chapter does not repeat those rules and should be read
as a **synthetic map** of the published dataset.




