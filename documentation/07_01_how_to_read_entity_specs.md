## 7. NeTEx entity specifications

This chapter describes, for each NeTEx entity used in the MVP, which attributes and sub-elements are retained, how they are constrained, and how they should be populated in the CFL dataset.

For each entity, the profile provides:

- A short functional description;
- A list of attributes and child elements used in the MVP;
- The expected cardinality in the CFL profile;
- Specific constraints or modelling notes;
- A minimal XML example.

The goal is to give producers and consumers a precise, operational view of what is *actually* used in the CFL NeTEx dataset.

---

### 7.1 How to read the specifications

Each entity in this chapter is presented using a standard structure that defines which NeTEx elements are used in the CFL MVP, how they are constrained, and how they must be populated.

---

#### 7.1.1 Structure of each entity specification

For each NeTEx entity, the specification includes:

- A **short functional description** of the entity;
- A **table of attributes and sub-elements** retained in the CFL MVP;
- The **allowed cardinality** in the CFL profile;
- **Constraints or modelling rules** specific to CFL;
- A **minimal XML example** to illustrate the expected structure.

---

#### 7.1.2 Meaning of table columns

Each table typically contains:

- **Element / Attribute**  
  NeTEx element or attribute name (e.g. `Name`, `QuayRef`, `DayTypeRef`).

- **Description**  
  A concise explanation of its meaning and use in the CFL context.

- **Cardinality (CFL MVP)**  
  Indicates whether the element is mandatory, optional or repeatable:  
  - `1..1` = mandatory, exactly one  
  - `0..1` = optional, at most one  
  - `1..n` = mandatory, one or more occurrences  
  - `0..n` = optional, any number of occurrences  

  The CFL cardinality may be **more restrictive** than the NeTEx standard; in such cases, the CFL cardinality is authoritative for data production.

- **Notes / Constraints** (optional)  
  Additional modelling rules (e.g. naming constraints, reference rules).

- **Example** (optional)  
  A sample value illustrating the concept.

---

#### 7.1.3 About examples and XML snippets

The examples provided in this chapter (sample values and minimal XML fragments):

- Are **illustrative only**,  
- Are **not real CFL production data**,  
- Serve exclusively to clarify the meaning of concepts and structures,  
- May not reflect final identifiers, codes, naming conventions, or operational data.

They are included to support understanding, not to prescribe actual content.
