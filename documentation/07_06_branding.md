# 7.6 Branding (ResourceFrame)

## 7.6.1 Purpose and scope

A **Branding** element defines the **visual identity** associated with a GroupOfLines.  
In the CFL profile, Branding is used to store:

- The **colour** of a CFL line family (e.g., L50 = ##A14D00),
- Optionally, other visual attributes in future extensions (logo, icon, etc.).

Branding does **not** carry operational meaning.  
It provides a reusable visual identity that may be applied across several NeTEx objects.

In the CFL MVP:

- **Branding is referenced by GroupOfLines** through `BrandingRef`;
- Individual **Lines inherit the colour** indirectly through their GroupOfLines;
- Only the **colour** is modelled at this stage.

---

## 7.6.2 Modelling principles

### Separation of visual identity

- Colour definitions belong to **Branding**, not to Lines or GroupOfLines directly.
- This ensures:
  - Clean separation between data (Line) and presentation (Branding),
  - Easier reuse of the same branding across multiple objects,
  - Future extensibility (logos, pictograms, accessibility variants, etc.).

### Identifier convention

Branding identifiers follow the CFL codespace:

`LU:CFL:Branding:<FamilyCode>`


Examples:

- `LU:CFL:Branding:L50`
- `LU:CFL:Branding:L10`

### Colour representation

Colours are expressed using the standard hexadecimal RGB format:

`#RRGGBB`


Examples:

- `#009BD4` (cyan/blue tone)  
- `#E52D2D` (red example)  

Only **one colour** is required in the CFL MVP.

Additional colour attributes (foreground, border, hover, etc.) may be added later.

---

## 7.6.3 Elements and attributes retained in the CFL MVP

| Element / Attribute | Description                     | Cardinality (CFL MVP) | Notes / Constraints                     | Example value                |
|---------------------|---------------------------------|------------------------|------------------------------------------|------------------------------|
| `@id`               | Unique identifier of Branding   | 1..1                   | Pattern `LU:CFL:Branding:<FamilyCode>`   | `LU:CFL:Branding:L50`        |
| `version`           | Object version                  | 1..1                   | Incremented when colour changes.         | `1`                           |
| `Name`              | Branding name                   | 1..1                   | SHOULD match the CFL family code.        | `L50`                        |
| `Colour`            | Main colour of the branding     | 1..1                   | HEX format `#RRGGBB`.                    | `#009BD4`                    |

### Notes

- Only one colour is modelled in the MVP.
- Branding MAY be extended with further visual properties in future lots (e.g. pictograms or light/dark variants).
- A Branding object is **typically linked to exactly one GroupOfLines**.

---

## 7.6.4 Rules and cardinalities

| Relationship / Rule           | Cardinality | Description                                                |
|-------------------------------|-------------|------------------------------------------------------------|
| Branding → Colour             | 1..1        | Each Branding SHALL define one main colour.               |
| GroupOfLines → BrandingRef    | 0..1 (SHOULD) | Each GroupOfLines SHOULD reference one Branding.        |
| Branding (per family code)    | 0..1        | Only one Branding SHALL exist per CFL family code.        |

---

## 7.6.5 XML example

```xml
<Branding id="LU:CFL:Branding:L50" version="1">
  <Name>L50</Name>
  <Colour>#009BD4</Colour>
</Branding>
```

And its usage inside a GroupOfLines:

```xml
<GroupOfLines id="LU:CFL:GroupOfLines:L50" version="1">
  <Name>L50 – Luxembourg – Arlon</Name>
  <BrandingRef ref="LU:CFL:Branding:L50"/>

  <members>
    <LineRef ref="LU:CFL:Line:8200001-8200027"/>
    <LineRef ref="LU:CFL:Line:8200027-8200103"/>
  </members>
</GroupOfLines>
```

