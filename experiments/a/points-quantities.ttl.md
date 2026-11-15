#   Brick 2.0 Design Notes - Points and Quantities

##  1. Scope

#.  Companion to [ontology.ttl.md](ontology.ttl.md), §10, Properties/Points.

#.  Property-first design based on [Klaus's sketch](../../sketches/properties-points.ttl), the design discussion, and our [point-attribute notes](../../point-attributes/point-attributes.md).

#.  [Room temperature example](room-temperature.ttl.md) contains one property with two points and correspondingly multiple access paths.

#.  Examples share prefixes and identifiers. Turtle blocks form one example graph.

    @prefix bro:  <https://ontology.brickschema.org/2.0/#bro:> .
    @prefix ex:   <https://example.org/building/> .
    @prefix qudt: <http://qudt.org/schema/qudt/> .
    @prefix qk:   <http://qudt.org/vocab/quantitykind/> .
    @prefix unit: <http://qudt.org/vocab/unit/> .
    @prefix owl:  <http://www.w3.org/2002/07/owl#> .
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
    @prefix xsd:  <http://www.w3.org/2001/XMLSchema#> .
    @prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
    @prefix sh:   <http://www.w3.org/ns/shacl#> .

##  2. Properties, Points, and Values

#.  A `Property` identifies an *intrinsic* attribute of an entity. The sound made by a tree falling in the forest. Inherits the rich/large "point" classifications from Brick's point class. Can have quantity kind, medium, constituent, mode, modifier, phenomenon, and enum kind. Can have a last-known value.

#.  A `Point` combines the source/channel identity and its access information, following `ontology.ttl.md`. It provides values for a `Property` and can be hosted by a controller. No subclasses yet.

#.  Addresses, protocol parameters, and storage identifiers live directly on the point. There is no separate external-reference node in this proposal.

#.  Sensing equipment remains a physical entity. Numeric values use `qudt:QuantityValue`.

    bro:Property                a owl:Class ; rdfs:subClassOf bro:Entity .
        bro:QuantifiableProperty a owl:Class ; rdfs:subClassOf bro:Property .
        bro:EnumerableProperty  a owl:Class ; rdfs:subClassOf bro:Property .
        bro:ObservableProperty  a owl:Class ; rdfs:subClassOf bro:Property .
        bro:ActuatableProperty  a owl:Class ; rdfs:subClassOf bro:Property .
    bro:Point                   a owl:Class ; rdfs:subClassOf bro:InformationObject .
    bro:SensorEquipment         a owl:Class ; rdfs:subClassOf bro:PhysicalEquipment .

#.  Give the property a specific type: for example, `Room_Air_Temperature_Sensor` for room temperature. That type is both observable and quantifiable. The final class hierarchy remains open.

### 2.1 Brick Classifications on Properties

#.  Retain existing Brick class names for former point classifications. For static attributes, use Klaus's `...Property` naming pattern: `ManufacturerNameProperty`, `SerialNumberProperty`, `AreaProperty`, and `RatedVoltageInputProperty`.

#.  `Sensor`, `Setpoint`, and `Alarm` classify the property's meaning. Physical sensing equipment remains `SensorEquipment`; the access component remains `Point`. Validation of these classifications is omitted here; §3.3 includes rules and validation for last-known values.

    bro:Sensor a owl:Class ; rdfs:subClassOf bro:ObservableProperty .
    bro:Setpoint a owl:Class ; rdfs:subClassOf bro:ActuatableProperty .
    bro:Alarm a owl:Class ; rdfs:subClassOf bro:EnumerableProperty .

    bro:Temperature_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor, bro:QuantifiableProperty .
    bro:Air_Temperature_Sensor a owl:Class ; rdfs:subClassOf bro:Temperature_Sensor .
    bro:Room_Air_Temperature_Sensor a owl:Class ; rdfs:subClassOf bro:Air_Temperature_Sensor .
    bro:Zone_Air_Temperature_Sensor a owl:Class ; rdfs:subClassOf bro:Air_Temperature_Sensor .
    bro:Average_Zone_Air_Temperature_Sensor a owl:Class ; rdfs:subClassOf bro:Zone_Air_Temperature_Sensor .
    bro:Air_Quality_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor .
    bro:Ammonia_Sensor a owl:Class ; rdfs:subClassOf bro:Air_Quality_Sensor, bro:QuantifiableProperty .
    bro:Angle_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor, bro:QuantifiableProperty .
    bro:Solar_Azimuth_Angle_Sensor a owl:Class ; rdfs:subClassOf bro:Angle_Sensor .
    bro:Pressure_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor, bro:QuantifiableProperty .
    bro:Air_Pressure_Sensor a owl:Class ; rdfs:subClassOf bro:Pressure_Sensor .
    bro:Differential_Pressure_Sensor a owl:Class ; rdfs:subClassOf bro:Pressure_Sensor .
    bro:Air_Differential_Pressure_Sensor a owl:Class ; rdfs:subClassOf bro:Air_Pressure_Sensor, bro:Differential_Pressure_Sensor .

    bro:Temperature_Setpoint a owl:Class ; rdfs:subClassOf bro:Setpoint, bro:QuantifiableProperty .
    bro:Cooling_Temperature_Setpoint a owl:Class ; rdfs:subClassOf bro:Temperature_Setpoint .
    bro:Occupied_Cooling_Temperature_Setpoint a owl:Class ; rdfs:subClassOf bro:Cooling_Temperature_Setpoint .
    bro:Unoccupied_Cooling_Temperature_Setpoint a owl:Class ; rdfs:subClassOf bro:Cooling_Temperature_Setpoint .
    bro:Status a owl:Class ; rdfs:subClassOf bro:ObservableProperty, bro:EnumerableProperty .
    bro:Mode_Status a owl:Class ; rdfs:subClassOf bro:Status .
    bro:Operating_Mode_Status a owl:Class ; rdfs:subClassOf bro:Mode_Status .
    bro:Contact_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor, bro:EnumerableProperty .
    bro:Occupancy_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor, bro:EnumerableProperty .
    bro:Hail_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor .
    bro:Frost_Sensor a owl:Class ; rdfs:subClassOf bro:Sensor, bro:Temperature_Sensor .
    bro:Communication_Loss_Alarm a owl:Class ; rdfs:subClassOf bro:Alarm .
    bro:Air_Alarm a owl:Class ; rdfs:subClassOf bro:Alarm .
    bro:Temperature_Alarm a owl:Class ; rdfs:subClassOf bro:Alarm .
    bro:Air_Temperature_Alarm a owl:Class ; rdfs:subClassOf bro:Air_Alarm, bro:Temperature_Alarm .

#.  This is a subset of the classification hierarchy. No class above inherits from `Point`. Quantity kind and medium remain explicit on property instances; class-driven inference is a separate decision.

### 2.2 Room Temperature Property with Point and Sensor Equipment

    ex:room101
        a bro:Space ;
        bro:hasProperty ex:room101AirTemperature .

    ex:room101AirTemperature
        a bro:Room_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air .

    ex:temperatureA
        a bro:Point ;
        bro:providesValueFor ex:room101AirTemperature ;
        bro:pointName "R101_TEMP_A" ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://123/analog-input,3/present-value" ;
        bro:hasProperty ex:temperatureAResolution .

    ex:temperatureAResolution
        a bro:ResolutionProperty ;
        qudt:hasQuantityKind qk:Temperature ;
        qudt:value 0.1 ;
        qudt:isDeltaQuantity true .

    ex:sensorEquipmentA
        a bro:SensorEquipment ;
        bro:hasPoint ex:temperatureA ;
        bro:hasProperty ex:sensorEquipmentASerialNumber .

    ex:sensorEquipmentASerialNumber
        a bro:SerialNumberProperty ;
        qudt:value "PROBE-A-772" .

    ex:controller7
        a bro:PhysicalEquipment ;
        bro:hostsPoint ex:temperatureA .

#.  Resolution's value, minimum, and maximum are shown in §5.2; both source points are shown in §5.5.

##  3. Associating Values with Properties

### 3.1 Simple Value

#. Simple values use `qudt:value` on the property instance.

    bro:ManufacturerNameProperty
        a owl:Class ;
        rdfs:subClassOf bro:Property .

    ex:pump1
        a bro:PhysicalEquipment ;
        bro:hasProperty ex:pump1ManufacturerName .

    ex:pump1ManufacturerName
        a bro:ManufacturerNameProperty ;
        qudt:value "Siemens" .
### 3.2 Value with a Unit

#. Attaching units to a value uses `qudt:quantityValue` with `qudt:hasUnit`. The quantity kind is identified on the property instance.
#. This allows a property to have multiple values, perhaps with different timestamps, see below, or from different sensors.
#. The quantity kind keeps the units compatible without requiring that all of the values have the same unit. 

    bro:AreaProperty
        a owl:Class ;
        rdfs:subClassOf bro:QuantifiableProperty .

    bro:RatedVoltageInputProperty
        a owl:Class ;
        rdfs:subClassOf bro:QuantifiableProperty .

    ex:roomStructured
        a bro:Space ;
        bro:hasProperty ex:roomArea .

    ex:roomArea
        a bro:AreaProperty ;
        qudt:hasQuantityKind qk:Area ;
        qudt:quantityValue [
            a qudt:QuantityValue ;
            qudt:hasUnit unit:M2 ;
            qudt:value 42.0
        ] .

    ex:motorWithProperty
        a bro:Motor ;
        bro:hasProperty ex:ratedVoltage .

    ex:ratedVoltage
        a bro:RatedVoltageInputProperty ;
        bro:description "Rated input voltage."@en ;
        qudt:hasQuantityKind qk:Voltage ;
        qudt:quantityValue ex:ratedVoltageValue .

    ex:ratedVoltageValue
        a qudt:QuantityValue ;
        qudt:hasUnit unit:V ;
        qudt:value 240.0 .

### 3.3 Value with a Unit and Timestamp

#. `bro:lastKnownValue` is a subproperty of `qudt:quantityValue` that also requires a timestamp.
#. Timestamped quantity values are inferred to be last-known values. More than one for a property produces a validation violation; the rules do not choose between them.

    bro:lastKnownValue rdfs:subPropertyOf qudt:quantityValue .

    # the object type of lastKnownValue is a QuantityValue with a timestamp
    bro:TimestampedQuantityValueShape a sh:NodeShape ;
        sh:class qudt:QuantityValue ;
        sh:property [
            sh:path bro:timestamp ;
            sh:minCount 1 ;
            sh:maxCount 1 ;
            sh:datatype xsd:dateTime
        ] .

    # Last-known values require a timestamp.
    bro:LastKnownValue a owl:Class, sh:NodeShape ;
        rdfs:subClassOf qudt:QuantityValue ;
        sh:targetObjectsOf bro:lastKnownValue ;
        sh:order 0 ;
        sh:node bro:TimestampedQuantityValueShape ;
        sh:rule [
            # Infer the LastKnownValue type from the lastKnownValue relationship.
            a sh:TripleRule ;
            sh:subject sh:this ;
            sh:predicate rdf:type ;
            sh:object bro:LastKnownValue
        ] .

    bro:LastKnownValueShape a sh:NodeShape ;
        sh:targetClass bro:Property ;
        sh:targetSubjectsOf qudt:quantityValue, bro:lastKnownValue ;
        sh:order 1 ;
        sh:rule [
            # Implement the subpropertyOf semantics in SHACL.
            a sh:TripleRule ;
            sh:subject sh:this ;
            sh:predicate qudt:quantityValue ;
            sh:object [ sh:path bro:lastKnownValue ]
        ] ;
        # There can only be one lastKnownValue for a property.
        sh:property [
            sh:path bro:lastKnownValue ;
            sh:maxCount 1 ;
            sh:class bro:LastKnownValue
        ] .

    # If a quantityValue has a timestamp, infer the lastKnownValue relationship.
    bro:InferLastKnownValueShape a sh:NodeShape ;
        sh:targetObjectsOf qudt:quantityValue ;
        sh:order 2 ;
        sh:rule [
            a sh:TripleRule ;
            sh:condition bro:TimestampedQuantityValueShape ;
            sh:subject [
                sh:path [ sh:inversePath qudt:quantityValue ]
            ] ;
            sh:predicate bro:lastKnownValue ;
            sh:object sh:this
        ], [
            # Also infer the LastKnownValue type for that quantityValue.
            a sh:TripleRule ;
            sh:condition bro:TimestampedQuantityValueShape ;
            sh:subject sh:this ;
            sh:predicate rdf:type ;
            sh:object bro:LastKnownValue
        ] .

    # Last-known values want to be named nodes (sh:Warning), so they can be
    # referenced by other properties, such as a point's lastKnownValue.
    bro:NamedLastKnownValueShape a sh:NodeShape ;
        sh:targetObjectsOf bro:lastKnownValue ;
        sh:severity sh:Warning ;
        sh:nodeKind sh:IRI ;
        sh:message "Give the last-known value an IRI so other properties or points can reference it." .

    ex:room102AirTemperature
        a bro:Room_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air ;
        qudt:quantityValue ex:room102TemperatureReading1200 ;
        bro:lastKnownValue ex:room102TemperatureReading1200 .

    ex:room102TemperatureReading1200
        a qudt:QuantityValue ;
        qudt:value 21.5 ;
        qudt:hasUnit unit:DEG_C ;
        bro:timestamp "2026-11-05T12:00:00Z"^^xsd:dateTime .

### 3.4 Enumerated Value

#. We use the enumeration pattern from 223. Each enum kind is a class; each named value is a class and an instance of itself.
#. A property with an enumerated value domain uses `bro:hasEnum` to identify the enum kind.
#. An enumeration covers both the 'quantity kind' and the 'unit' of a property's values.


    bro:EnumerationKind a owl:Class .
    bro:EnumerationKind-Operating_Mode a owl:Class ; rdfs:subClassOf bro:EnumerationKind .
    bro:EnumerationKind-Occupancy a owl:Class ; rdfs:subClassOf bro:EnumerationKind .

    bro:Occupancy-Occupied
        a owl:Class, bro:Occupancy-Occupied ;
        rdfs:subClassOf bro:EnumerationKind-Operating_Mode, bro:EnumerationKind-Occupancy .
    bro:Occupancy-Unoccupied
        a owl:Class, bro:Occupancy-Unoccupied ;
        rdfs:subClassOf bro:EnumerationKind-Operating_Mode, bro:EnumerationKind-Occupancy .

    bro:DefaultOperatingModeProperty
        a owl:Class ;
        rdfs:subClassOf bro:EnumerableProperty .

    ex:controller7
        a bro:PhysicalEquipment ;
        bro:hasProperty ex:controllerDefaultMode .

    ex:controllerDefaultMode
        a bro:DefaultOperatingModeProperty ;
        bro:hasEnum bro:EnumerationKind-Operating_Mode ;
        qudt:value bro:Occupancy-Occupied .

### 3.5 Enumerated Value with a Timestamp

    ex:room101
        a bro:Space ;
        bro:hasProperty ex:roomOccupancy .

    ex:roomOccupancy
        a bro:Occupancy_Sensor ;
        bro:hasEnum bro:EnumerationKind-Occupancy ;
        bro:lastKnownValue ex:roomOccupancyReading1200 .

    ex:roomOccupancyReading1200
        qudt:value bro:Occupancy-Occupied ;
        bro:timestamp "2026-11-13T12:00:00Z"^^xsd:dateTime .

### 3.6 Property without a Value

    ex:pump2
        a bro:PhysicalEquipment ;
        bro:hasProperty ex:pump2ManufacturerName .

    ex:pump2ManufacturerName a bro:ManufacturerNameProperty .
### 3.7 Getting Additional Values through a Point

    ex:room102
        a bro:Space ;
        bro:hasProperty ex:room102AirTemperature .

    ex:room102TemperaturePoint
        a bro:Point ;
        bro:providesValueFor ex:room102AirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://123/analog-input,5/present-value" .

    ex:controller7 bro:hostsPoint ex:room102TemperaturePoint,
        ex:manufacturerNamePoint .

    ex:manufacturerNamePoint
        a bro:Point ;
        bro:pointName "B01_Flr02_Pump1_MnftrNm" ;
        bro:providesValueFor ex:pump1ManufacturerName ;
        bro:providesValueOfType xsd:string ;
        bro:hasAddress "bacnet://123/45,678/90" .

### 3.8 Values from Several Sources

    ex:temperatureA
        bro:providesValueFor ex:room101AirTemperature ;
        bro:lastKnownValue ex:temperatureReading1200 .

    ex:temperatureReading1200
        a qudt:QuantityValue ;
        qudt:value 22.4 ;
        qudt:hasUnit unit:DEG_C ;
        bro:timestamp "2026-11-05T12:00:00Z"^^xsd:dateTime .

#.  💢 *Open Issue:* Define how a property's last-known-value record identifies its supplying point when several points provide values. Do not silently choose one source's reading as authoritative.

#.  💢 *Open Issue:* Identify which access system supplied a reading when a point has multiple access paths, and distinguish capture time from reading time.

##  4. Property Semantics

#.  For Phenomenon as a supertype of Substance, see [§4.5 below](#phenomenon).

#.  Keep Brick's familiar types, then state details such as Air, Occupied, and Average explicitly on the property.

#.  Use `qudt:hasQuantityKind` for quantity kind, `bro:ofMedium` and `bro:ofConstituent` for material context, `bro:inMode` for applicability, `bro:measurementModifier` for selection/aggregation, `bro:hasPhenomenon` for the subject, and `bro:hasEnum` for a categorical value domain.

#.  `inMode` and `hasEnum` follow the [local CSV](../../point-attributes/points.csv) and [enumeration draft](../../point-attributes/issue-drafting/enumeration-kinds.md). `measurementModifier` and `hasPhenomenon` are proposed predicates for dimensions in [the attribute notes](../../point-attributes/point-attributes.md).

#.  Enum kinds form a class hierarchy. Following the 223 construction, each named enum value is also a class and an instance of itself. Occupied and Unoccupied are enum kinds under both Operating_Mode and Occupancy. Names include the enum kind, such as `Occupancy-Unoccupied`, `Measurement_Modifier-Average`, and `Phenomenon-Solar`. Vocabulary comes from our notes and Brick; wire encodings are separate.

    bro:EnumerationKind-Measurement_Modifier a owl:Class ; rdfs:subClassOf bro:EnumerationKind .
    bro:EnumerationKind-Phenomenon a owl:Class ; rdfs:subClassOf bro:EnumerationKind .
    bro:EnumerationKind-Open_Close a owl:Class ; rdfs:subClassOf bro:EnumerationKind .
    bro:EnumerationKind-Communication_Status a owl:Class ; rdfs:subClassOf bro:EnumerationKind .

    bro:inMode a owl:ObjectProperty ;
        rdfs:domain bro:Property ; rdfs:range bro:EnumerationKind-Operating_Mode .
    bro:measurementModifier a owl:ObjectProperty ;
        rdfs:domain bro:Property ; rdfs:range bro:EnumerationKind-Measurement_Modifier .
    bro:hasPhenomenon a owl:ObjectProperty ;
        rdfs:domain bro:Property ; rdfs:range bro:EnumerationKind-Phenomenon .
    bro:hasEnum a owl:ObjectProperty ;
        rdfs:domain bro:EnumerableProperty ; rdfs:range owl:Class .

#.  `inMode`, `measurementModifier`, and `hasPhenomenon` identify specific enum kinds, using their self-typed values. `hasEnum` identifies the broader kind defining the property's value domain. Enum membership follows the subclass hierarchy; semantic annotations are not automatically copied from property classes.

### 4.1 Quantity Kind and Medium

#.  Move the semantic attributes from the point-attribute notes onto property instances. `qudt:hasQuantityKind` identifies the kind of numeric value; `bro:ofMedium` identifies the medium whose property is described.

    ex:room101
        a bro:Space ;
        bro:hasProperty ex:room101AirTemperature .

    ex:room101AirTemperature
        a bro:Room_Air_Temperature_Sensor ;
        bro:name "Room 101 representative air temperature"@en ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air .

### 4.2 Constituent and Medium

#.  Follow the [Open223 constituent model](https://docs.open223.info/medium-mixtures/): `bro:ofConstituent` identifies the constituent being quantified; `bro:ofMedium` identifies the medium containing it. Use a quantity kind matching the reported concentration basis.

    ex:room101 bro:hasProperty ex:ammoniaFraction .

    ex:ammoniaFraction
        a bro:Ammonia_Sensor ;
        qudt:hasQuantityKind qk:AmountOfSubstanceFraction ;
        bro:ofConstituent bro:NH3 ;
        bro:ofMedium bro:Air ;
        bro:description "Ammonia mole fraction in air."@en .

#.  This example reports ammonia mole fraction. Mass concentration and volume fraction require different quantity kinds; constituent and medium retain their separate roles.

### 4.3 Operating Mode


    bro:Operating_Mode-LoadShed
        a owl:Class, bro:Operating_Mode-LoadShed ;
        rdfs:subClassOf bro:EnumerationKind-Operating_Mode .

#.  The notes name Occupied, Unoccupied, and LoadShed. These qualify which property applies; they do not assert the current operating mode.

    ex:room101 bro:hasProperty ex:occupiedCoolingSetpoint,
        ex:unoccupiedCoolingSetpoint, ex:loadShedCoolingSetpoint, ex:roomOperatingMode .

    ex:occupiedCoolingSetpoint
        a bro:Occupied_Cooling_Temperature_Setpoint ;
        bro:inMode bro:Occupancy-Occupied ;
        bro:ofMedium bro:Air ;
        qudt:hasQuantityKind qk:Temperature .

    ex:unoccupiedCoolingSetpoint
        a bro:Unoccupied_Cooling_Temperature_Setpoint ;
        bro:inMode bro:Occupancy-Unoccupied ;
        bro:ofMedium bro:Air ;
        qudt:hasQuantityKind qk:Temperature .

    ex:loadShedCoolingSetpoint
        a bro:Cooling_Temperature_Setpoint ;
        bro:inMode bro:Operating_Mode-LoadShed ;
        bro:ofMedium bro:Air ;
        qudt:hasQuantityKind qk:Temperature .

    ex:roomOperatingMode
        a bro:Operating_Mode_Status ;
        bro:hasEnum bro:EnumerationKind-Operating_Mode ;
        bro:lastKnownValue [
            qudt:value bro:Occupancy-Occupied ;
            bro:timestamp "2026-11-13T12:00:00Z"^^xsd:dateTime
        ] .

#.  `roomOperatingMode` reports a timestamped state; `occupiedCoolingSetpoint` describes a setpoint applicable in that mode.

#.  Occupancy and load shedding may be independent axes. Grouping these values follows the notes; mode combinations, precedence, and schedules remain open.

### 4.4 Measurement Modifier

#.  Use the modifiers in the notes. Average, Maximum, Minimum, and Peak need a time window or population; Coldest and Warmest need the candidate locations or values.

    bro:Measurement_Modifier-Average
        a owl:Class, bro:Measurement_Modifier-Average ;
        rdfs:subClassOf bro:EnumerationKind-Measurement_Modifier .
    bro:Measurement_Modifier-Maximum
        a owl:Class, bro:Measurement_Modifier-Maximum ;
        rdfs:subClassOf bro:EnumerationKind-Measurement_Modifier .
    bro:Measurement_Modifier-Minimum
        a owl:Class, bro:Measurement_Modifier-Minimum ;
        rdfs:subClassOf bro:EnumerationKind-Measurement_Modifier .
    bro:Measurement_Modifier-Coldest
        a owl:Class, bro:Measurement_Modifier-Coldest ;
        rdfs:subClassOf bro:EnumerationKind-Measurement_Modifier .
    bro:Measurement_Modifier-Warmest
        a owl:Class, bro:Measurement_Modifier-Warmest ;
        rdfs:subClassOf bro:EnumerationKind-Measurement_Modifier .
    bro:Measurement_Modifier-Peak
        a owl:Class, bro:Measurement_Modifier-Peak ;
        rdfs:subClassOf bro:EnumerationKind-Measurement_Modifier .

    ex:room101 bro:hasProperty ex:meanTemperature, ex:maximumTemperature, ex:minimumTemperature .

    ex:meanTemperature
        a bro:Average_Zone_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air ;
        bro:measurementModifier bro:Measurement_Modifier-Average ;
        bro:description "Arithmetic mean air temperature over the preceding 15-minute interval."@en .

    ex:maximumTemperature
        a bro:Room_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air ;
        bro:measurementModifier bro:Measurement_Modifier-Maximum ;
        bro:description "Maximum room air temperature over the preceding 15-minute interval."@en .

    ex:minimumTemperature
        a bro:Room_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air ;
        bro:measurementModifier bro:Measurement_Modifier-Minimum ;
        bro:description "Minimum room air temperature over the preceding 15-minute interval."@en .

#.  A source for a derived property provides values for that property, rather than for the instantaneous temperature property. The modifier identifies the operation; the description specifies the calculation and scope. Formal window and operand relationships remain open.

### 4.5 Phenomenon <a id="phenomenon"></a>

#.  Phenomenon is proposed as a supertype of Substance.

#.  The notes list Solar, Hail, Frost, Noise, Contact, and People. A phenomenon may have a numeric result or an enumerated result. It does not determine the output unit or datatype.

    bro:Phenomenon-Solar
        a owl:Class, bro:Phenomenon-Solar ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .
    bro:Phenomenon-Hail
        a owl:Class, bro:Phenomenon-Hail ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .
    bro:Phenomenon-Frost
        a owl:Class, bro:Phenomenon-Frost ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .
    bro:Phenomenon-Noise
        a owl:Class, bro:Phenomenon-Noise ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .
    bro:Phenomenon-Contact
        a owl:Class, bro:Phenomenon-Contact ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .
    bro:Phenomenon-People
        a owl:Class, bro:Phenomenon-People ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .

    ex:site1 a bro:Site ; bro:hasProperty ex:solarAzimuth, ex:hailMeasurement .

    ex:solarAzimuth
        a bro:Solar_Azimuth_Angle_Sensor ;
        bro:hasPhenomenon bro:Phenomenon-Solar ;
        qudt:hasQuantityKind qk:Angle ;
        bro:description "Solar azimuth measured clockwise from true north."@en .

    ex:hailMeasurement
        a bro:Hail_Sensor ;
        bro:hasPhenomenon bro:Phenomenon-Hail ;
        bro:description "Hail measurement; size, intensity, or damage-potential quantity kind must be specified by the source model."@en .

    ex:filter1 bro:hasProperty ex:frostCondition .

    ex:frostCondition
        a bro:Frost_Sensor ;
        bro:hasPhenomenon bro:Phenomenon-Frost ;
        bro:description "Frost or conditions that may cause frost; output representation must be specified by the source model."@en .

    bro:Open_Close-Open
        a owl:Class, bro:Open_Close-Open ;
        rdfs:subClassOf bro:EnumerationKind-Open_Close .
    bro:Open_Close-Closed
        a owl:Class, bro:Open_Close-Closed ;
        rdfs:subClassOf bro:EnumerationKind-Open_Close .

    ex:door101 a bro:PhysicalEquipment ; bro:hasProperty ex:doorContact .

    ex:doorContact
        a bro:Contact_Sensor ;
        bro:hasPhenomenon bro:Phenomenon-Contact ;
        bro:hasEnum bro:EnumerationKind-Open_Close ;
        bro:lastKnownValue [
            qudt:value bro:Open_Close-Closed ;
            bro:timestamp "2026-11-13T12:00:00Z"^^xsd:dateTime
        ] .

    ex:room101 bro:hasProperty ex:roomOccupancy .

    ex:roomOccupancy
        a bro:Occupancy_Sensor ;
        bro:hasPhenomenon bro:Phenomenon-People ;
        bro:hasEnum bro:EnumerationKind-Occupancy ;
        bro:lastKnownValue ex:roomOccupancyReading1200 .

#.  Occupancy detected in a room and its scheduled operating mode are distinct properties, even when both use the words Occupied and Unoccupied.

#.  Noise remains a vocabulary member until the property specifies what is measured, such as sound pressure or sound level. No quantity kind is guessed from the word Noise.

### 4.6 Differences

#.  Distinguish a difference from an absolute reading; identify its operands and sign convention.

    ex:filter1 a bro:PhysicalEquipment ; bro:hasProperty ex:airPressureDifference .

    ex:airPressureDifference
        a bro:Air_Differential_Pressure_Sensor ;
        bro:ofMedium bro:Air ;
        qudt:hasQuantityKind qk:Pressure ;
        qudt:isDeltaQuantity true ;
        bro:description "Upstream air pressure minus downstream air pressure across filter 1."@en .

#.  [QUDT's `isDeltaQuantity`](https://qudt.org/schema/qudt/isDeltaQuantity) identifies a quantity representing a change or interval. Apply it to the intrinsic property here; it is independent of the point's reporting unit. The description identifies the operands and sign convention.

##  5. Points and Access

### 5.1 External Access through a Point

#.  A point carries its access information directly. §3.7 shows how it supplies values; the sections below cover addresses, point attributes, and multiple sources.

    ex:room102
        a bro:Space ;
        bro:hasProperty ex:room102AirTemperature .

    ex:room102AirTemperature
        a bro:Room_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air .

    ex:room102TemperaturePoint
        a bro:Point ;
        bro:providesValueFor ex:room102AirTemperature ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://123/analog-input,5/present-value" .

#.  This address identifies a BACnet object and its present-value property. The point represents the accessible value, rather than the entire BACnet object. Its other fields, such as resolution, can have their own properties (§5.2).

### 5.2 Properties of a Point

#.  Reuse `bro:hasProperty` for attributes of the point. `bro:providesValueFor` identifies the building property it exposes; `bro:hasProperty` identifies the point's own attributes.

#.  These proposed property classes describe the point's exposed range and resolution. Numeric values below are illustrative, not supplied by Klaus.

    bro:MinimumValueProperty
        a owl:Class ;
        rdfs:subClassOf bro:QuantifiableProperty .

    bro:MaximumValueProperty
        a owl:Class ;
        rdfs:subClassOf bro:QuantifiableProperty .

    bro:ResolutionProperty
        a owl:Class ;
        rdfs:subClassOf bro:QuantifiableProperty .

    ex:temperatureA
        a bro:Point ;
        bro:pointName "R101_TEMP_A" ;
        bro:providesValueFor ex:room101AirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasProperty ex:temperatureAMinimum,
                        ex:temperatureAMaximum,
                        ex:temperatureAResolution .

    ex:temperatureAMinimum
        a bro:MinimumValueProperty ;
        qudt:hasQuantityKind qk:Temperature ;
        qudt:quantityValue [
            a qudt:QuantityValue ;
            qudt:value -20.0 ;
            qudt:hasUnit unit:DEG_C
        ] .

    ex:temperatureAMaximum
        a bro:MaximumValueProperty ;
        qudt:hasQuantityKind qk:Temperature ;
        qudt:quantityValue [
            a qudt:QuantityValue ;
            qudt:value 80.0 ;
            qudt:hasUnit unit:DEG_C
        ] .

    ex:temperatureAResolution
        a bro:ResolutionProperty ;
        qudt:hasQuantityKind qk:Temperature ;
        qudt:isDeltaQuantity true ;
        qudt:quantityValue [
            a qudt:QuantityValue ;
            qudt:value 0.1 ;
            qudt:hasUnit unit:DEG_C
        ] .

#.  Minimum and maximum describe the values exposed by this point. Resolution describes the smallest increment, so it is a delta quantity. These do not describe room operating limits or physical sensor accuracy.

#.  Each metadata property can have an inline value, an unknown value, or its own supplying point through `bro:providesValueFor`. A separate point is needed only when describing external access to that metadata value.

### 5.3 Sensor Equipment and Hosting

#.  In the complete example in §5.5, `bro:hasPoint` associates sensor equipment with its point; `bro:hostsPoint` identifies the controller exposing it. These describe different relationships.

#.  Equipment serial numbers use the same static-property pattern as manufacturer names (§3.1).

    bro:SerialNumberProperty
        a owl:Class ;
        rdfs:subClassOf bro:Property .

### 5.4 Multiple Access Systems and Readers

#.  One source/channel may be available through BACnet and a historian. Put both sets of access fields on the point; here the historian stores the same samples in Celsius.

    ex:temperatureA
        bro:hasAddress "bacnet://123/analog-input,3/present-value" ;
        bro:storedAt ex:historian ;
        bro:hasTimeseriesId "room101-temperature-a" .

#.  Multiple applications reading the same point do not imply additional points. Independently measured or calculated values need their own source identity.

### 5.5 Independent Points for One Property

#.  Two independent sources can provide values for the same identified property. Each point declares its own unit; their units need not match. Repeat the shared property and both access paths here.

    ex:room101
        a bro:Space ;
        bro:hasProperty ex:room101AirTemperature .

    ex:room101AirTemperature
        a bro:Room_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air .

    ex:temperatureA
        a bro:Point ;
        bro:pointName "R101_TEMP_A" ;
        bro:providesValueFor ex:room101AirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://123/analog-input,3/present-value" .

    ex:temperatureB
        a bro:Point ;
        bro:pointName "R101_TEMP_B" ;
        bro:providesValueFor ex:room101AirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://123/analog-input,4/present-value" .

    ex:controller7
        a bro:PhysicalEquipment ;
        bro:hostsPoint ex:temperatureA, ex:temperatureB .

    ex:sensorEquipmentA
        a bro:SensorEquipment ;
        bro:hasProperty ex:sensorEquipmentASerialNumber ;
        bro:hasLocation ex:room101 ;
        bro:hasPoint ex:temperatureA .

    ex:sensorEquipmentASerialNumber
        a bro:SerialNumberProperty ;
        qudt:value "PROBE-A-772" .

    ex:sensorEquipmentB
        a bro:SensorEquipment ;
        bro:hasProperty ex:sensorEquipmentBSerialNumber ;
        bro:hasLocation ex:room101 ;
        bro:hasPoint ex:temperatureB .

    ex:sensorEquipmentBSerialNumber
        a bro:SerialNumberProperty ;
        qudt:value "PROBE-B-884" .

#.  The shared target is an explicit modeling assertion. Same room and quantity kind alone do not establish the same property.

##  6. Enumerated Values and Alarms

#.  Communication statuses and alarms can share one enum kind. The alarm additionally identifies which states are abnormal.

    ex:controller7 bro:hasProperty ex:communicationAlarm .

    bro:Communication_Status-Lost
        a owl:Class, bro:Communication_Status-Lost ;
        rdfs:subClassOf bro:EnumerationKind-Communication_Status .
    bro:Communication_Status-Established
        a owl:Class, bro:Communication_Status-Established ;
        rdfs:subClassOf bro:EnumerationKind-Communication_Status .

    ex:communicationAlarm
        a bro:Communication_Loss_Alarm ;
        bro:hasEnum bro:EnumerationKind-Communication_Status ;
        bro:lastKnownValue [
            qudt:value bro:Communication_Status-Established ;
            bro:timestamp "2026-11-13T12:00:00Z"^^xsd:dateTime
        ] ;
        bro:description "Lost is the alarm condition; established is normal."@en .

    ex:communicationPoint
        a bro:Point ;
        bro:providesValueFor ex:communicationAlarm ;
        bro:providesValueOfType xsd:string .

#.  A temperature alarm concerns temperature while returning a state. `qudt:hasQuantityKind` describes what it concerns; it does not declare a numeric output datatype.

    ex:room101 bro:hasProperty ex:airTemperatureAlarm .

    ex:airTemperatureAlarm
        a bro:Air_Temperature_Alarm ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air ;
        bro:description "Air temperature alarm: normal or high."@en .

#.  💢 *Open Issue:* Define validation that a property's reported value belongs to its `bro:hasEnum` kind.

#.  💢 *Open Issue:* Define how to annotate which enum values are normal or abnormal for an alarm.

#.  💢 *Open Issue:* Define mappings from protocol encodings to semantic enum values. Do not infer protocol integers from enum member names.
