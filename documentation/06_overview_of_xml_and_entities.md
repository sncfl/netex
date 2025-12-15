# 6. Overview of XML Publications and Contained Entities

This section provides a consolidated overview of the XML files produced in the CFL NeTEx MVP and identifies which Frames and entities each file contains.  
It serves as a navigation aid for readers before entering the detailed entity specifications in sections 6.1 onwards.

The goal is to give a **clear and immediate understanding** of how the dataset is structured and how the various parts of the profile relate to each other at publication time.

---

## 6.1 Publication files and content overview

The MVP dataset consists of **three XML files**:

- `resource.xml`  
- `stop.xml`  
- `line_<LineId>.xml` (one file per published Line)

Each file contains one or more NeTEx Frames within a `PublicationDelivery`.  
The table below summarises the Frames and the entities they include.

---

### Table — XML files, frames and entities

| **XML file** | **Frames included** | **Entities contained** | **Notes** |
|--------------|---------------------|--------------------------|----------|
| **resource.xml** | `ResourceFrame` | Operator, Network, Branding, Codespaces | Dataset-wide reference objects shared by all publications. |
| | `ServiceCalendarFrame` | DayType, OperatingPeriod, DayTypeAssignment | Calendar and validity definitions applied across all Lines. |
| | *(optional)* `ServiceFrame` | Interchange / transfer-related entities | Included only if shared interchange information is published. |
| **stop.xml** | `SiteFrame` | StopPlace, Quay | Complete stop infrastructure model. Contains no timetable data. |
| **line_<LineId>.xml** | `ServiceFrame` | Line, ScheduledStopPoint, StopPointInJourneyPattern, ServiceJourneyPattern, VehicleJourney | Operational structures specific to a single Line. |
| | `TimetableFrame` | TimetabledPassingTime, VehicleJourneyStopAssignment | Timing information and platform assignments for the VehicleJourneys. |

---

## 6.2 Rationale for the file organisation

The separation into three files follows NeTEx best practices and supports modularity and maintainability:

- **resource.xml** centralises identifiers, operators, and calendars that remain stable over time.  
- **stop.xml** isolates the stop infrastructure model, which evolves independently of timetables.  
- **line_<LineId>.xml** groups all operational and timetable data for a single Line, allowing incremental updates and simplifying debugging.

This organisation ensures that:

- producers can regenerate only the Line files when schedules change,  
- updates to the stop model do not affect vehicle schedules,  
- consumers can load only the relevant components,  
- version management remains controlled and predictable.

---

## 6.3 Using this overview

This table provides a reference point for interpreting the rest of Chapter 6:

- It indicates **where each entity appears** within the published dataset.  
- It helps developers and integrators locate objects quickly during ingestion or debugging.  
- It reinforces the link between **Frames** (chapter 4) and **entity specifications** (chapter 6).

---
