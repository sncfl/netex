# 6. Overview of XML Publications and Contained Entities

This chapter provides a **synthetic overview** of the XML files produced in the CFL NeTEx MVP
and identifies **where each Frame and entity is physically published**.

It complements the conceptual description of Frames
([4. Frames description](04_frames_description.md))
by mapping them to the concrete XML publications,
and supports the reading of the detailed NeTEx entity specifications
([7. How to read entity specifications](07_01_how_to-read_entity_specs.md)).

This chapter does **not** define new modelling rules.
The authoritative rules for file organisation, publication structure,
and cross-file references are defined in
([3. Data Organisation](03_data_organisation.md)).

---

## 6.1 XML files, Frames and entities

The CFL NeTEx MVP dataset is published as a set of XML files.
Each file contains one or more NeTEx Frames encapsulated in a `PublicationDelivery`.

The table below summarises, for each XML file,
the Frames it contains and the NeTEx entities **contained** in that file.

| **XML file** | **Frames included** | **Entities contained** | **Notes** |
|--------------|---------------------|------------------------|----------|
| **resource.xml** | `ResourceFrame` | Operator, Branding, Codespaces | Dataset-wide reference objects shared by all publications. |
| | `ServiceCalendarFrame` | DayType, OperatingPeriod, DayTypeAssignment | Calendar and validity definitions applied across all Lines. |
| | *(optional / future)* `ServiceFrame` | Interchange / transfer-related entities | Not published in the MVP baseline. If added later, shared interchange data is published here (as recommended by experts). |
| **stop.xml** | `SiteFrame` | StopPlace, Quay | Complete stop infrastructure model. Contains no timetable data. |
| **line_<LineId>.xml** | `ServiceFrame` | Line, ScheduledStopPoint, StopPointInJourneyPattern, ServiceJourneyPattern, VehicleJourney | Operational structures specific to a single Line. |
| | `TimetableFrame` | TimetabledPassingTime, VehicleJourneyStopAssignment | Timing information and (if used) platform/stop assignment structures for VehicleJourneys. |

Note: Entities listed in this table are specified in Chapter 7 when they are effectively used in the MVP.
Optional or future entities may be documented later when they are introduced.

---

This overview table serves as a **synthetic map** of the published dataset.
Detailed modelling rules, constraints and XML examples for each entity
are provided exclusively in **Chapter 7**.
