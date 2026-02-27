# 6. Overview of XML Publications and Contained Entities

This chapter provides a **synthetic overview** of the XML files produced in the CFL NeTEx MVP and identifies **where each Frame and entity is physically published**.

It complements the conceptual description of Frames ([4. Frames description](04_frames_description.md)) by mapping them to the concrete XML publications, and supports the reading of the detailed NeTEx entity specifications ([7. NeTEx entity specifications](07_01_entity_specification_conventions.md)).

This chapter does **not** define new modelling rules.  
The authoritative rules for file organisation, publication structure, and cross-file references are defined in [3. Data Organisation](03_data_organisation.md).

---

## 6.1 XML files, Frames and entities

The CFL NeTEx MVP dataset is published as a set of XML files.  
Each file contains one or more NeTEx Frames encapsulated in a `PublicationDelivery`.

The table below summarises, for each XML file, the Frames it contains and the NeTEx entities **contained** in that file.

| **XML file** | **Frames included** | **Entities contained** | **Notes** |
|--------------|---------------------|------------------------|----------|
| **resource.xml** | `ResourceFrame` | [Codespace](07_05_shared_reference_entities_index.md#codespace), [Operator](07_05_shared_reference_entities_index.md#operator), [Branding](07_05_shared_reference_entities_index.md#branding), [GroupOfLines](07_03_network_index.md#groupoflines-resourceframe) | Dataset-wide reference objects shared by all publications. |
|  | `ServiceCalendarFrame` | [DayType](07_05_shared_reference_entities_index.md#daytype-servicecalendarframe), [OperatingPeriod](07_05_shared_reference_entities_index.md#operatingperiod-servicecalendarframe), [DayTypeAssignment](07_05_shared_reference_entities_index.md#daytypeassignment-servicecalendarframe) | Calendar and validity definitions applied across all Lines. |
|  | *(optional / future)* `ServiceFrame` | Interchange / transfer-related entities | Not published in the MVP baseline. If added later, shared interchange data will be published here. |
| **stop.xml** | `SiteFrame` | [StopPlace](07_02_stops_and_places_index.md#stopplace-siteframe), [Quay](07_02_stops_and_places_index.md#quay-siteframe) | Authoritative physical stop infrastructure (stations/stop areas and boarding locations). Contains no timetable data. |
| **line_<LineId>.xml** | `ServiceFrame` | [Line](07_03_network_index.md#line-serviceframe), [ScheduledStopPoint](07_02_stops_and_places_index.md#scheduledstoppoint-serviceframe), [PassengerStopAssignment](07_02_stops_and_places_index.md#passengerstopassignment-serviceframe), [StopPointInJourneyPattern](07_04_timetable_index.md#stoppointinjourneypattern-serviceframe), [ServiceJourneyPattern](07_04_timetable_index.md#servicejourneypattern-serviceframe), *(optional)* `ServiceLink` | Logical service structure for one Line. Mapping from logical stops to physical stops is expressed via PassengerStopAssignment. |
|  | `TimetableFrame` | [VehicleJourney](07_04_timetable_index.md#vehiclejourney-timetableframe), [TimetabledPassingTime](07_04_timetable_index.md#timetabledpassingtime-timetableframe), *(optional)* [VehicleJourneyStopAssignment](07_04_timetable_index.md#vehiclejourneystopassignment-timetableframe) | Scheduled journeys and times. VehicleJourneyStopAssignment is only used when quay/platform assignment is known per journey. |

Note: Entities listed in this table are specified in Chapter 7 when they are effectively used in the MVP.  
Optional or future entities will be documented later as they are introduced.

---

This overview table serves as a **synthetic map** of the published dataset.  
Detailed modelling rules, constraints and XML examples for each entity are provided exclusively in **Chapter 7**.

