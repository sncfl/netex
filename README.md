# CFL NeTEx Profile

This workspace brings together all elements of the **CFL NeTEx Profile**, designed to harmonise and structure the modelling of timetable data used within CFL.  
The profile is based on the CEN standards (NeTEx, Transmodel) while reflecting CFL’s operational requirements.

---

## Structure

This repository contains two main areas :


### `documentation/`
This folder contains all documentation related to the Luxembourg profile, structured into thematic subdirectories:

- **00_introduction/**  
  General principles  
  Cross-cutting rules  
  Data organisation  
  Conventions  
  Publication principles  

- **01_specifications/**  
  Detailed content of the CFL profile, including:  
  - definitions of the modelled entities  
  - structural rules  
  - naming conventions  
  - modelling constraints  
  - rules applying to timetable and real-time data (NeTEx and SIRI)  
  - upcoming extensions (accessibility, fares, new modes)  

This content will be published progressively according to the timeline of the various work packages.

### `examples/`
This folder hosts practical XML examples illustrating the modelling rules defined in the documentation, such as:

- baseline deliveries  
- timetable updates  
- bikebox modelling  
- other examples aligned with future extention of the scope

These examples serve as reference material for implementers and internal CFL teams.

---

## Governance

The content of this repository reflects the modelling work carried out as part of the **CFL NeTEx Profile** initiative.

Questions, clarification requests, and proposals for improvements can be submitted via the repository’s Issues.  
Decisions relating to profile evolution are discussed in dedicated working sessions and incorporated into subsequent releases.

---

## Contributions

### Participation

Anyone interested may:

- ask questions  
- suggest improvements  
- report inconsistencies  
- propose content evolutions

Discussions take place primarily through Issues.

### Branches

- **main**  
  - README  
  - minor fixes (spelling, grammar, small clarifications)  
  - light structural or formatting adjustments  

- **next**  
  - substantial evolutions  
  - new sections  
  - major reorganisations  
  - significant updates to the profile

### Branch synchronisation

- **Frequency** : monthly  
- **Responsible** : repository maintainer  
- **Scope** : merging `main` into `next` for minor updates and structural alignments  

---

## Technical maintenance

Technical maintenance of the repository (folder organisation, file structure, path consistency) is handled by the team responsible for the CFL profile.  
The functional content is based on the analyses performed within the project and aligned with the NeTEx/SIRI standards.
