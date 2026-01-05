# 6. Overview of XML Publications and Contained Entities

This chapter provides a consolidated overview of the XML files produced in the CFL NeTEx MVP
and identifies which **Frames** and **entities** each file contains.

It serves as a navigation aid for readers before entering the detailed entity specifications
in sections 6.1 onwards.

The goal is to give a **clear and immediate understanding** of:
- how the dataset is split into XML publications,
- how Frames are distributed across files,
- where each entity defined in **[Chapter 4 – Frames description](04_frames_description.md)** is physically published.

---

## 6.1 Publication files and content overview

The CFL NeTEx MVP dataset consists of **three types of XML files**:

- `resource.xml`
- `stop.xml`
- `line_<LineId>.xml` (one file per published Line)

Each file contains one or more NeTEx Frames encapsulated in a `PublicationDelivery`.
The table below summarises, for each XML file, the Frames included and the entities they contain.

---

### Table — XML files, frames and entities

| **XML file** | **Frames included** | **Entities contained (MVP)** | **Notes** |
|--------------|---------------------|------------------------------|----------|
| **resource.xml** | `ResourceFrame` | Codespace, Operator, Notice, ValueSet | Dataset-wide reference data shared by all publications. |
| | `ServiceCalendarFrame` | DayType, OperatingPeriod, OperatingDay, DayTypeAssignment | Centralised calendar model used by all VehicleJourneys. |
| **stop.xml** | `SiteFrame` | StopPlace, Quay | Authoritative stop and platform referential. No service or timetable data. |
| **line_<LineId>.xml** | `ServiceFrame` | Line, JourneyPattern, StopPointInJourneyPattern | Logical service structure for a single Line. |
| | `TimetableFrame` | VehicleJourney, PassingTime | Scheduled circulations and their planned times at each stop. |


---

## 6.2 Rationale for the file organisation

The separation into these XML files follows NeTEx best practices and supports modularity,
maintainability and controlled updates:

- **resource.xml** centralises reference data and calendars that are stable and reused across all Lines.
- **stop.xml** isolates the stop infrastructure model, which evolves independently from services and timetables.
- **line_<LineId>.xml** groups all service structure and timetable data for a single Line.

This organisation ensures that:
- Line timetables can be updated independently without republishing stop or calendar data,
- stop infrastructure updates do not impact timetable files,
- consumers can load only the files relevant to their use case,
- versioning and debugging remain predictable and scoped.


---

## 6.3 Using this overview

This overview table is the entry point for interpreting the rest of this documentation :

- it indicates **in which XML file each entity is published**,
- it helps developers and integrators locate entities during ingestion, validation or debugging,
- it provides the concrete link between:
- the **conceptual Frames described in
    [Chapter 4 – Frames description](04_frames_description.md)**, and
  - the **entity-level specifications described in
    [Chapter 7 - How to read entity specifications](07_01_how_to-read_entity_specs.md)**.

---


