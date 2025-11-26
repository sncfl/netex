# 2. General principles

## 2.1 What is NeTEx (in simple terms)

NeTEx (Network Timetable Exchange) is a **CEN Technical Specification series** (CEN/TS 16614-x) for exchanging public-transport network and timetable data. It provides:

- a **shared vocabulary** (entities and relationships) for stops, networks, timetables, fares, etc.;
- a **machine-readable format** (XML) to publish and exchange those data consistently;
- a **modular structure** (frames) so producers publish only what is needed.

NeTEx is **model-driven** (Transmodel): you describe the world (stops, lines, journeys) and then export consistent data sets from that model.

---

## 2.2 Why NeTEx?

Public-transport information in Europe has historically been published through multiple formats, each created for a specific operational or national context (e.g. GTFS, VDV, TransXChange). While these formats cover particular needs, they also lead to fragmentation and limited interoperability between systems.

NeTEx was developed by CEN to provide a **harmonised, multimodal and extensible standard** capable of supporting:

- the complete description of the public-transport offer (stops, networks, timetables, accessibility, fares);
- consistent and reliable data exchange between operators, authorities, and information systems;
- seamless cross-border and international interoperability.

NeTEx does not replace existing formats but **complements** them:

- **GTFS** remains widely used for consumer-facing journey planners and global map services;
- **TAP TSI** remains mandatory for railway timetable and reservation exchanges at European level;
- **SIRI** provides the real-time counterpart to NeTEx for operational monitoring and passenger information.

Compared to other formats, NeTEx stands out by its **model-driven foundation** (Transmodel), its **completeness** across all public-transport domains, and its **extensibility** through national profiles.
GTFS focuses on lightweight consumer applications; TAP TSI targets rail-sector exchanges; SIRI covers real-time.
NeTEx acts as the **structured backbone** from which lighter or specialised formats can be derived or complemented.

---

## 2.3 What is a “profile” and why CFL needs one

A NeTEx profile defines how the standard is applied within a specific national, regional or organisational context.
It selects the relevant concepts, constrains optionalities, and establishes clear modelling and coding rules.

A profile ensures:

- coherence across datasets produced by different systems or teams;
- predictability for data consumers, who can rely on stable structures and identifiers;
- interoperability with other national or European datasets by aligning with common modelling principles.

Without a profile, each producer or consumer could interpret NeTEx differently, leading to inconsistencies or incompatible implementations.

The CFL profile provides a shared, authoritative reference for structuring and publishing timetable data, ensuring consistency across CFL systems and preparing for future multimodal or cross-operator integration in Luxembourg.

---

## 2.4 Method and delivery approach

The CFL NeTEx profile is developed through a **pragmatic, iterative and collaborative process**:

- **Alignment with European standards**: strict adherence to NeTEx (CEN/TS 16614-x) and Transmodel (EN 12896) to ensure compatibility at European level.
- **Reuse of established practices**: integration of relevant modelling choices from existing profiles (e.g. France, Nordic, ITxPT) to ensure continuity with recognised implementations.
- **Regular expert consultations**: workshops with CFL business, data and technical teams to validate modelling decisions.
- **Transparency and documentation**: systematic recording of decisions, modelling choices and rationale.
- **Incremental refinement**: the MVP delivers a functional baseline which is progressively enriched based on actual use cases.
- **Semantic versioning (MAJOR.MINOR.PATCH)**: ensuring traceability and controlled evolution of the profile.

This approach ensures that the profile remains both **normative enough** to guarantee interoperability and **practical enough** to be produced, consumed and maintained efficiently.

---
## 2.5 Principles adopted for the MVP

The work package 1 establishes a **Minimum Viable Profile** focused on CFL’s scheduled offer.
Its design follows the principles below:

- **Standards compliance**: modelling choices follow NeTEx and Transmodel, unless a justified deviation is documented.
- **Modularity**: the dataset is structured into focused, thematic frames to ensure maintainability and clarity.
- **Multimodality by design**: the same modelling rules apply to all CFL-operated modes (rail, bus, funicular, autonomous shuttles).
- **Sufficiency without over-specification**: only the elements necessary for stable production and consumption are included in the MVP; additional details are deferred to future work packages.
- **Clarity of presentation**: concepts are introduced progressively, with examples to support understanding.
- **Stability and persistence**: identifiers, versioning rules and temporal validity are designed to remain stable across updates, ensuring long-term data integrity.

These principles ensure that WP1 is both **operationally usable** and **robust enough** to support future extensions.
