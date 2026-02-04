<!-- markdown-toc-disable -->
# Frequently Asked Questions (FAQ) - CFL NeTEx Profile

This FAQ aims to document the **design choices, modelling principles and scope decisions** underlying the CFL NeTEx profile.

It is intended to:
- Clarify recurring questions raised during design and review phases,
- Explain modelling decisions in a transparent and pedagogical way,
- Support understanding and adoption of the profile by a broad audience (operators, implementers, experts, institutions).

This FAQ is a **living document**.  
It will be **progressively enriched** as the profile evolves, new use cases emerge, or additional questions are identified.  
Questions may also be **reworded, regrouped or reordered** over time to improve readability and coherence.

The current structure reflects the state of discussions and arbitrages at the time of writing and does not imply a fixed or final organisation.

## Questions
- [Frequently Asked Questions (FAQ) - CFL NeTEx Profile](#frequently-asked-questions-faq---cfl-netex-profile)
  - [Questions](#questions)
  - [Why does the CFL NeTEx profile use CompositeFrame?](#why-does-the-cfl-netex-profile-use-compositeframe)
  - [Why is NetworkFrame not included in the CFL MVP NeTEx profile?](#why-is-networkframe-not-included-in-the-cfl-mvp-netex-profile)
  - [What does Line represent in the CFL NeTEx profile, and how are designations such as “Line 50” or “Line 70” handled?](#what-does-line-represent-in-the-cfl-netex-profile-and-how-are-designations-such-as-line-50-or-line-70-handled)
  - [Why does the CFL MVP NeTEx profile model VehicleJourney but not ServiceJourney?](#why-does-the-cfl-mvp-netex-profile-model-vehiclejourney-but-not-servicejourney)
  - [Why is the CFL NeTEx data split across multiple XML files?](#why-is-the-cfl-netex-data-split-across-multiple-xml-files)
  - [Why does the CFL NeTEx profile use Codespaces, and how should they be applied?](#why-does-the-cfl-netex-profile-use-codespaces-and-how-should-they-be-applied)
  - [Why are routes not modelled at this stage in the CFL NeTEx profile?](#why-are-routes-not-modelled-at-this-stage-in-the-cfl-netex-profile)
  - [Why is no bitcode used for timetable publication in the CFL NeTEx profile?](#why-is-no-bitcode-used-for-timetable-publication-in-the-cfl-netex-profile)


## Why does the CFL NeTEx profile use CompositeFrame?

In NeTEx, several mechanisms can be used to structure data publications, in particular `GeneralFrame` and `CompositeFrame`, which serve **different but complementary purposes**.

A `GeneralFrame` is used to group NeTEx objects belonging to the **same thematic or functional domain**. This approach is notably used in the French NeTEx profile, where data are organised into domain-oriented `GeneralFrame` instances (for example common elements, network-related data, timetable data or calendar data), identified through specific `TypeOfFrame` values. This structuring relies on a shared understanding of these domains and is well suited to a mature, nationally coordinated profile.

A `CompositeFrame`, by contrast, is used as a **container of frames**. It assembles several business frames into a single publication structure, while keeping each frame explicitly identified and semantically independent. `CompositeFrame` makes the overall organisation of the publication directly visible, without requiring prior knowledge of domain conventions or profile-specific typing.

In the CFL NeTEx profile, `CompositeFrame` is used as the primary structuring mechanism. This choice allows the different business frames (such as `ResourceFrame`, `SiteFrame`, `ServiceFrame`, `ServiceCalendarFrame` and `TimetableFrame`) to be clearly separated and explicitly assembled within a single publication.

This approach provides several benefits:
- Immediate readability of the publication structure;
- clear attribution of responsibilities to each frame;
- Reduced reliance on implicit thematic conventions;
- Progressive enrichment of the profile without restructuring existing data.

The CFL profile does not ignore the domain-oriented approach adopted in the French profile. Rather, it deliberately adopts a different structuring strategy at this stage, in order to favour **clarity, pedagogy and ease of adoption**, particularly in a context where multiple operators are expected to progressively align their practices and gain familiarity with NeTEx concepts.

This choice does not preclude future evolutions. Domain-oriented structuring using `GeneralFrame` may be introduced later, as the NeTEx ecosystem matures and broader alignment or multi-operator governance structures are established.

## Why is NetworkFrame not included in the CFL MVP NeTEx profile?

`NetworkFrame` is not included in the CFL MVP NeTEx profile as a deliberate **scope and layering decision**.

In NeTEx, `NetworkFrame` is intended to describe **network-oriented concepts** that provide a transversal view of a transport network, independently of individual service offers. Such concepts become particularly relevant when multiple operators publish services on a shared territory and a common network representation is required.

At the current stage, the CFL MVP profile focuses on:
- Publication of the timetable offer,
- Passenger information needs (SIV),
- Clear and stable passenger-facing service structures,
- Interoperability with real-time systems such as SIRI.

These objectives are fully addressed through service- and timetable-oriented frames (notably `ServiceFrame`, `TimetableFrame` and `SiteFrame`), without introducing an explicit network layer.

Introducing `NetworkFrame` at this stage would require additional assumptions regarding network governance, responsibilities and level of detail, which are not yet necessary to support the identified use cases. Excluding `NetworkFrame` therefore allows the profile to:
- Remain focused on passenger-facing use cases,
- Avoid premature infrastructure or network abstractions,
- Keep a clear separation between service modelling and network modelling,
- Limit complexity for data producers and consumers.

Network-oriented concepts, including `NetworkFrame`, are expected to become more relevant in a future **multi-operator context**, where a shared and harmonised network representation may be required across multiple service providers. This perspective is acknowledged, but considered **out of scope for the CFL MVP NeTEx profile**.

## What does Line represent in the CFL NeTEx profile, and how are designations such as “Line 50” or “Line 70” handled?

In the CFL NeTEx profile, a `Line` represents a **commercial service axis**, defined by an origin, a destination and, where relevant, intermediate “via” stations (e.g., *Luxembourg – Wasserbillig*).  
It is a passenger-facing concept used to structure timetable information and service presentation.

In the CFL context, and in particular within the SIV, the term *commercial service* refers to a **passenger-facing structuring of the timetable offer**. It reflects how services are presented and understood by passengers, typically through a clear origin–destination axis, and provides a stable entry point for timetable consultation, journey planning and passenger information.

A commercial service is independent of:
- Service category (RE, RB, IC, etc.),
- Rolling stock,
- Detailed routing or infrastructure usage,
- Internal operational or railway-specific classifications.

The notion of commercial service is therefore not related to commercial or financial aspects, but to the **functional presentation of the service offer to passengers**.  
In the CFL NeTEx profile, this concept is modelled through the `Line`, which provides the passenger-facing structure of the offer.

A NeTEx `Line` is **not equivalent** to designations such as *Line 50* or *Line 70*.  
These designations are historical and functional railway references commonly used in the Luxembourg context. They are associated with a consistent **visual identity** (notably a specific colour) and are primarily used to group services for passenger information and map representation.

In the CFL NeTEx profile, these designations are therefore modelled as `GroupOfLines`.  
A `GroupOfLines` allows several `Line` objects to be grouped under a shared identity, without conflating this grouping with the definition of a single commercial service axis.

This distinction makes it possible to:
- Keep the `Line` focused on a clear and explicit commercial service axis,
- Preserve well-known historical and visual groupings (e.g. colours),
- Avoid ambiguity between service structure, service category and railway heritage concepts.

Service categories such as RE, RB, IC or TGV are not used as `Line` identifiers (`PublicCode`) because they describe the **service category or product** of a journey, not the commercial axis itself.  
On a given `Line`, several service categories may coexist depending on the operated journey or the period. In the CFL profile, these codes are therefore handled as **service classifications associated with journeys**, while the `Line` remains the stable, passenger-facing grouping.


## Why does the CFL MVP NeTEx profile model VehicleJourney but not ServiceJourney?

In the CFL MVP NeTEx profile, `VehicleJourney` is used as the primary journey entity because it best matches the **functional objectives** of the profile at this stage.

A `VehicleJourney` represents a **concrete, operated journey**, with a defined timetable, stop sequence and operating day. This level of abstraction directly supports the main use cases targeted by the CFL MVP profile, in particular:
- Publication of the timetable offer,
- Passenger information systems (SIV),
- Consistency with real-time information based on SIRI,
- Alignment with existing CFL operational data sources.

`ServiceJourney`, by contrast, introduces an additional abstraction layer intended to represent a service independently of its operational realisation. While this concept is fully valid in NeTEx and Transmodel, it is not required to fulfil the current objectives of the CFL MVP profile.

Modelling only `VehicleJourney` at this stage allows the profile to:
- Remain simple and readable,
- Avoid unnecessary duplication between abstract and operated journeys,
- Reduce modelling and implementation complexity,
- Focus on passenger-facing and operationally stable data.

This choice does not imply that `ServiceJourney` is incompatible with the CFL model. It reflects a **deliberate MVP scope decision**. The introduction of `ServiceJourney`, potentially alongside `VehicleJourney`, may be considered in future profile extensions if additional use cases require a clearer separation between abstract services and their operated instances.

## Why is the CFL NeTEx data split across multiple XML files?

In practice, NeTEx publications are almost always delivered as a ZIP archive containing several XML files, as a single XML file would quickly become too large and difficult to handle. This approach is also used in other national and operator profiles.

The key question for the CFL NeTEx profile is therefore not whether to split the data, but **how to split it**.

The chosen file structure follows a recommendation from an external NeTEx expert, based on an analysis of the datasets published by the ATP on data.gouv.lu. This analysis made it possible to assess the **nature of the data, their level of complexity, and the relationships between concepts** to be modelled.

Based on this assessment, the expert proposed a split reflecting **distinct functional scopes** (such as reference data, stops and sites, service definitions, calendars and timetables), while keeping closely related concepts together. The objective was to find a **balanced level of granularity**: sufficient to keep files readable and well-scoped, but limited enough to avoid unnecessary fragmentation.

This specific split reflects the structure that was recommended given the data analysed and the current scope of the CFL NeTEx profile. Other valid splits could exist, but this one was identified as the most appropriate to represent the Luxembourg datasets in a clear and manageable way.


## Why does the CFL NeTEx profile use Codespaces, and how should they be applied?

In NeTEx, identifiers play a central role: they are used to reference objects across frames, across XML files and, potentially, across datasets produced by different organisations. A `codespace` provides a **stable and explicit namespace** that ensures identifiers remain unique, unambiguous and interpretable outside their original production context.

Using codespaces addresses several key needs:
- **Uniqueness**: preventing identifier collisions when multiple datasets are combined or exchanged.
- **Clarity**: making the origin and governance of an identifier explicit (who defines it and who is responsible for it).
- **Interoperability**: enabling consistent referencing across files, frames and systems, including downstream consumers such as the NAP.

In the CFL NeTEx profile, codespaces are therefore introduced for identifiers that are expected to play a **structuring role** in the data model. This typically applies to identifiers that:
- Are referenced across multiple frames or XML files,
- Are intended to be stable over time,
- Are exposed to external consumers,
- Or represent shared reference objects (such as stops and sites, lines, journeys, calendars or other core entities).

Conversely, the profile deliberately avoids introducing a codespace for every identifier. For identifiers that are purely local in scope, short-lived, or used only for technical convenience within a single file or frame, adding a codespace would not improve interoperability and would unnecessarily increase complexity.

The use of codespaces in the CFL NeTEx profile therefore follows a **pragmatic and purpose-driven approach**. Codespaces are introduced when they bring clear benefits in terms of governance, stability and reusability, and omitted when they do not. This ensures that identifiers remain both robust and readable, without overengineering the model.

This approach also supports future evolution. As datasets are combined, reused or extended, codespaces provide a clear mechanism to manage identifier ownership and avoid ambiguity, while leaving flexibility in how different producers organise their internal identifiers.

## Why are routes not modelled at this stage in the CFL NeTEx profile?

In NeTEx, routes and route-related elements can be used to describe the detailed path followed by services between stops. While these concepts are part of the standard and are relevant for certain use cases, they are not modelled in the CFL NeTEx profile at this stage.

This choice reflects the **current objectives and scope** of the profile. The CFL NeTEx profile primarily focuses on publishing the timetable offer and supporting passenger information use cases, where the sequence of stops and scheduled times are the key elements. These needs are fully addressed through `Line`, `JourneyPattern`, `VehicleJourney` and associated timetable elements, without requiring an explicit route model.

Introducing routes would add an additional modelling layer that:
- Is not required for the identified passenger-facing use cases,
- Would increase the complexity of the data model,
- Would require additional assumptions about the level of geometric or topological detail to be provided,
- Could introduce dependencies on infrastructure-related data that are out of scope at this stage.

The decision not to model routes does not imply that they are irrelevant or excluded by principle. It reflects a **deliberate sequencing choice**: prioritising concepts that are essential for timetable publication and passenger information before introducing more detailed network or path representations.

Route modelling may be considered in future evolutions of the profile if new use cases emerge, such as advanced network analysis, multimodal routing, or stronger alignment with infrastructure-oriented datasets. At the current stage, omitting routes helps keep the profile focused, understandable and easier to implement.

## Why is no bitcode used for timetable publication in the CFL NeTEx profile?

NeTEx is designed as a **semantic and explicit data exchange format**, based on the Transmodel conceptual model. It relies on clearly typed objects, explicit relationships and readable values, rather than on compact or encoded representations.

The NeTEx standard does not define a generic mechanism for publishing timetable data using bitcodes or binary encodings. Concepts such as calendars, operating days, exceptions and service validity are modelled through explicit structures and attributes, in line with NeTEx interoperability principles.

The CFL NeTEx profile therefore does not use bitcodes for timetable publication, as this approach is **not part of the NeTEx standard** and would introduce non-standard decoding logic for data consumers.

Instead, the profile follows the standard NeTEx modelling approach, favouring clarity, interoperability and long-term maintainability. Data volume and exchange constraints are addressed through structural mechanisms (such as frame organisation, file splitting and ZIP packaging), rather than through encoded representations.
