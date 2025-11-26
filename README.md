# CFL NeTEx Profile

This repository brings together all elements of the **CFL NeTEx Profile**.  
Its purpose is to document and structure the modelling of timetable and real-time data used within CFL, based on the CEN standards (NeTEx, Transmodel) and adapted to Luxembourg’s operational needs.

---

## Structure

The repository is organised into two main areas:

### `documentation/`
Contains all written material related to the Luxembourg profile.  
The content is structured as **numbered Markdown files**, each describing a specific topic (general principles, data organisation, stops, timetable, accessibility, real-time components, etc.).

Two subfolders provide supporting material:

- **`media/`** — images and visual assets used in the documentation  
- **`diagrams/`** — diagram source files and exported figures

The documentation is updated progressively as the profile evolves.

### `examples/`
Contains practical XML examples illustrating how the modelling rules are applied.  
These examples may include:

- baseline deliveries  
- timetable updates  
- bikebox modelling  
- other examples added as the scope expands

They serve as reference material for implementers and internal CFL teams.

---

## Governance

The content of this repository reflects the modelling work carried out for the CFL NeTEx Profile.  
Questions, requests for clarification and improvement proposals can be submitted via the repository’s Issues.  
Decisions on profile evolution are discussed in dedicated working sessions and integrated into subsequent updates.

---

## Contributions

### Participation
Contributors may:

- ask questions  
- signal issues or inconsistencies  
- propose improvements  
- suggest content evolutions

Discussion takes place primarily through Issues.

### Branches

- **main**  
  - README and general information  
  - minor edits (formatting, spelling, clarifications)  
  - light structural adjustments  

- **next**  
  - substantial updates  
  - new chapters  
  - major reorganisations  
  - significant content additions

### Branch synchronisation

- **Frequency**: monthly  
- **Performed by**: repository maintainer  
- **Scope**: merge changes from `main` into `next` to keep both branches aligned

---

## Technical maintenance

Technical maintenance (structure, links, file organisation) is handled by the team responsible for the CFL profile.  
The functional content is based on the modelling work conducted within the project and follows the NeTEx and SIRI standards.

