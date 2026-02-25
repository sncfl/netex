# 7.5 Shared reference entities (ResourceFrame)

## Overview

The **ResourceFrame** contains shared reference entities that are reused across the entire CFL NeTEx MVP dataset.

Unlike most other entities described in Chapter 7, which are documented in dedicated sections, ResourceFrame entities are **grouped in this section** because they:

- Are transversal by nature;
- Are not tied to a specific service, stop or timetable;
- And are published together in `resource.xml`.

Each ResourceFrame entity used in the CFL MVP is described in a dedicated subsection below and can be referenced individually from other parts of the documentation.

The ResourceFrame is published exclusively in `resource.xml`.

---

## Codespace

### Functional description

A **Codespace** defines a namespace used to qualify identifiers (`id`) in the dataset.  
It ensures global uniqueness and enables unambiguous cross-file referencing.

All identifiers used in the CFL NeTEx MVP dataset rely on Codespaces declared in the ResourceFrame.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|----------------------|
| `@id` | Identifier of the Codespace | 1..1 | Stable identifier defined by CFL or national governance. |
| `Xmlns` | XML namespace URI | 1..1 | Must be unique. |
| `XmlnsUrl` | Documentation URL | 0..1 | Optional. |
| `Description` | Human-readable description | 0..1 | Informative only. |

---

### CFL-specific modelling rules

- All identifiers used in the dataset **must reference a declared Codespace**.
- The CFL MVP uses the codespace **`LU:CFL`**.
- Codespaces are stable and must not change across deliveries.
- Future LU-level codespaces (e.g. for other operators) will be added in the same ResourceFrame.

---

## Operator

### Functional description

The **Operator** represents the organisation responsible for operating the services described in the dataset.

In the CFL NeTEx MVP, a single Operator is defined and reused across all Lines and services.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|----------------------|
| `@id` | Identifier of the Operator | 1..1 | Must follow the CFL identifier scheme. |
| `Name` | Official operator name | 1..1 | Human-readable name. |
| `ShortName` | Short label | 0..1 | Optional, if used consistently. |
| `ContactDetails` | Contact information | 0..1 | Optional, minimal use in MVP. |

---

### CFL-specific modelling rules

- The CFL MVP defines **exactly one Operator**.
- All Line and service entities reference this Operator using `OperatorRef`.
- Operator identifiers are stable and never reused.

---

## Branding

### Functional description

**Branding** defines visual or commercial branding information that may be associated with services or Lines.

It enables downstream systems to apply a consistent visual identity (e.g., colours, logos) when presenting services to passengers.

---

### Elements and attributes retained in the CFL MVP

| Element / Attribute | Description | Cardinality (CFL MVP) | Notes / Constraints |
|---------------------|-------------|------------------------|----------------------|
| `@id` | Identifier of the Branding | 1..1 | Stable identifier. |
| `Name` | Branding name | 1..1 | Human-readable label. |
| `Description` | Description of branding | 0..1 | Optional. |

---

### CFL-specific modelling rules

- Branding entities are defined **once** in the ResourceFrame.
- Branding may be referenced from Line or service entities if required.
- Branding usage remains optional in the MVP and may be expanded later.

---

## Optional / future ResourceFrame entities

The following ResourceFrame entities are **not part of the CFL MVP baseline**, but may be introduced in future extensions of the profile.

They are listed here to document their **expected location and role** when introduced.

---

### Notice

**Notice** is used to provide shared textual information (e.g., warnings, informational messages, detailed information on accessibility) that may be referenced by multiple entities across Frames.

---

### ValueSet and TypeOfValue

**ValueSet** and **TypeOfValue** are used to define controlled vocabularies and categorical value lists shared across the dataset.

They may be introduced when formal value governance is required.

---

### Interchange and transfer-related entities

If shared interchange or transfer information is published, the corresponding entities shall be defined in a **ServiceFrame within `resource.xml`**, as recommended by NeTEx experts.

These entities are not included in the CFL MVP baseline.

---

## Scope limitations and summary

The ResourceFrame in the CFL NeTEx MVP:

- Does not model networks or organisational hierarchies beyond the Operator;
- Does not include fare-related reference data;
- Does not include accessibility metadata (covered by later work packages);
- Does not include real-time or operational reference entities.

The ResourceFrame provides the **shared foundation** of the CFL NeTEx MVP dataset by ensuring:

- Consistent identifier namespaces;
- A single authoritative Operator definition;
- Reusable branding information;
- And a clear extension point for future shared reference data.

All other Frames rely on the ResourceFrame for stable, dataset-wide references.

