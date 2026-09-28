# Brick 2.0 Wish List

### Scope

* An incremental improvement to Brick 1.x with some breaking changes, not a complete remake

### Clean up "design mistakes"

- ...
- ...

### Complete the technical merger with RealEstateCore (REC)

- Unify common concepts, such as `rec:Collection` and `brick:Collection` or `rec:feeds` and `brick:feeds`
- Publish one ontology with one prefix and one namespace IRI
- Use one naming convention consistently (PascalCase or Snake_Case)
- ...

### Rebase Brick on ASHRAE Standard 223

- Option 1: literally extend the 223 ontology
    - *Pro:* do not have to reinvent what is already there
    - *Con:* 223 insists on a lot of detail via its SHACL shapes that Brick 1.x doesn't insist on
- Option 2: duplicate the major concepts of 223 into Brick (and define a trivial alignment)
    - *Pro:* allow more freedom (e.g., every 223 model could be trivially converted to a Brick 2.0 model, but not every Brick 2.0 model converted-to-223 would necessarily pass 223's stricter SHACL shapes)
    - *Con:* needs a conversion step

### Made for AI

- Favor designs that make instance data easily digestible for LLMs via MCP

### Clean up

- Exclusively use QUDT quantity kinds in favor of Brick's (add missing ones to QUDT if needed)
- Revise the `brick:Substance` hierarchy in Brick (align with 223; add phenomena like wind, noise, etc.)
- Split `rec:Architecture` into physical and logical spaces (cf. `s223:PhysicalSpace`/`s223:DomainSpace`)
- Disentangle `brick:Entity`/`brick:Class`
- ...

### New features

- The semantics of every `brick:Point` subclass should be fully described through semantic annotations
    - (In addition to or in place of `brick:Tag`?)
- Define enumerations of the possible values for `brick:Point` subclasses describing states (e.g., on/off/auto)
- ...

### Built on a solid foundation

- RDF (rdf:), RDF Schema (rdfs:), and XML Schema Datatypes (xsd:)
- SHACL for validation, documentation, and basic reasoning
- QUDT for quantity kinds and units
- GeoSPARQL for geospatial data
