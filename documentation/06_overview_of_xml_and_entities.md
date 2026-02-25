# 6. Overview of XML Publications and Contained Entities

This chapter provides a **synthetic overview** of the XML files produced in the CFL NeTEx MVP and identifies **where each Frame and entity is physically published**.

It complements the conceptual description of Frames ([4. Frames description](04_frames_description.md)) by mapping them to the concrete XML publications, and supports the reading of the detailed NeTEx entity specifications ([7. How to read entity specifications](07_01_how_to-read_entity_specs.md)).

This chapter does **not** define new modelling rules.
The authoritative rules for file organisation, publication structure, and cross-file references are defined in ([3. Data Organisation](03_data_organisation.md)).

---

## 6.1 XML files, Frames and entities

The CFL NeTEx MVP dataset is published as a set of XML files.
Each file contains one or more NeTEx Frames encapsulated in a `PublicationDelivery`.

The table below summarises, for each XML file, the Frames it contains and the NeTEx entities **contained** in that file.

| **XML file** | **Frames included** | **Entities contained** | **Notes** |
|--------------|---------------------|------------------------|----------|
| **stop.xml** | `SiteFrame` | [StopPlace](07_02_stopplace.md), [Quay](07_03_quay.md) | Complete stop infrastructure model. Contains no timetable data. |
| **line_<LineId>.xml** | `ServiceFrame` | [Line](07_04_line.md), [GroupOfLines](07_05_groupoflines.md), [ScheduledStopPoint](07_07_scheduledstoppoint.md), [StopPointInJourneyPattern](07_08_stoppointinjourneypattern.md), [ServiceJourneyPattern](07_09_servicejourneypattern.md), [VehicleJourney](07_10_vehiclejourney.md) | Operational structures specific to a single Line. |
| | `TimetableFrame` | [TimetabledPassingTime](07_14_timetabledpassingtime.md), [VehicleJourneyStopAssignment](07_15_vehiclejourneystopassignment.md) | Timing information and (if used) platform/stop assignment structures for VehicleJourneys. |
| **resource.xml** | `ResourceFrame` | [Operator](07_16_resourceframe.md#7163-operator), [Branding](07_16_resourceframe.md#7164-branding), [Codespace](07_16_resourceframe.md#7162-codespace) | Dataset-wide reference objects shared by all publications. |
| | `ServiceCalendarFrame` | [DayType](07_11_daytype.md), [OperatingPeriod](07_12_operatingperiod.md), [DayTypeAssignment](07_13_daytypeassignment.md) | Calendar and validity definitions applied across all Lines. |
| | *(optional / future)* `ServiceFrame` | Interchange / transfer-related entities | Not published in the MVP baseline. If added later, shared interchange data will be published here. |


Note: Entities listed in this table are specified in Chapter 7 when they are effectively used in the MVP.
Optional or future entities will be documented later as they are introduced.

---

This overview table serves as a **synthetic map** of the published dataset.
Detailed modelling rules, constraints and XML examples for each entity are provided exclusively in **Chapter 7**.


