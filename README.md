# CFL NeTEx Profile

This repository brings together all elements of the **CFL NeTEx Profile**.

Its purpose is to document and structure the modelling of timetable and related public transport data used within CFL, based on the **CEN standards** (NeTEx, Transmodel) and adapted to Luxembourg’s operational needs.

This profile provides a shared reference for CFL internal systems, partners and implementers, and serves as a foundation for data exchange and potential publication, including through the National Access Point (NAP).

> ⚠️ This repository documents a **CFL profile**.  
> It is **not a national Luxembourg NeTEx profile** and does not replace any national or European normative specification.

---

## Structure

The repository is organised into two main areas:

### `documentation/`

Contains all written material related to the CFL NeTEx Profile.

The content is structured as numbered Markdown files, each describing a specific topic, such as:
- general principles;
- data organisation;
- stops and stations;
- network and lines;
- timetable modelling;
- accessibility;
- real-time related components;
- cross-cutting modelling rules.

The documentation is updated progressively as the profile evolves.

Diagrams are embedded directly within the Markdown documentation using code-based representations (e.g. Mermaid), ensuring consistency, traceability and ease of maintenance.

---

### `examples/`

Contains practical XML examples illustrating how the modelling rules defined in the profile are applied.

These examples may include:
- baseline deliveries;
- timetable updates;
- bikebox modelling;
- other examples added as the scope expands.

They serve as reference material for implementers and internal CFL teams.

---

## Governance

The content of this repository reflects the modelling work carried out within CFL projects related to timetable and passenger information.

Questions, requests for clarification and improvement proposals can be submitted via the repository **Issues**.

Decisions regarding profile evolution are discussed during dedicated CFL working sessions and integrated progressively into the documentation.  
The profile may evolve based on operational feedback, interoperability needs and alignment with European initiatives.

---

## Contributions

### Participation

Contributors may:
- ask questions;
- report issues or inconsistencies;
- propose clarifications or improvements;
- suggest evolutions of the profile.

Discussions take place primarily through **Issues**.

---

### Branches

The repository follows a two-branch approach:

#### `main`
- README and general information;
- minor edits (formatting, spelling, clarifications);
- light structural adjustments.

#### `next`
- substantial content updates;
- new chapters or major revisions;
- significant reorganisations;
- major additions to documentation or examples.

---

### Branch synchronisation

To maintain consistency between branches:

- **Frequency:** monthly  
- **Performed by:** repository maintainer  
- **Scope:** merge changes from `main` into `next` to keep both branches aligned

---

## Technical maintenance

Technical maintenance of the repository (structure, links, file organisation) is handled by the team responsible for the CFL NeTEx Profile.

The functional content is based on CFL modelling work and follows the NeTEx and Transmodel standards.  
This repository does not constitute an official normative or regulatory document.
