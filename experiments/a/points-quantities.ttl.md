#   Brick 2.0 Design Notes - Points and Quantities

##  1. Scope

#.  Companion to [ontology.ttl.md](ontology.ttl.md), §10, Properties/Points.

#.  Property-first design based on [this sketch](../../sketches/properties-points.ttl), the design discussion, and our [point-attribute notes](../../point-attributes/point-attributes.md).

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

#.  A `Property` identifies an *intrinsic* attribute of an entity. The sound made by a tree falling in the forest. Inherits the rich/large "point" classifications from Brick's point class. Can have quantity kind, medium, constituent, mode, modifier, and phenomenon. Can have a last-known value. Enum kinds describe the value records (§3.4).

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

#.  Resolution's value, minimum, and maximum are shown in §5.2; both source points are shown in §5.4.

##  3. Associating Values with Properties

### 3.1 Simple Value

#. Simple values use `qudt:value` on the property instance.

    bro:ManufacturerNameProperty
        a owl:Class ;
        rdfs:subClassOf bro:Property .

    bro:SerialNumberProperty
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

#. We use the enumeration pattern from 223. Each enum kind is a class; each named value is a class and "punned" as an instance of itself.
#. Each value record uses `bro:hasEnumKind` to identify its enumeration. This takes the place of the unit; not clear what the 'quantity kind' would be.
#. Different points/observers can use different enumerations for the same property, so each value has its own enum kind.


    bro:EnumerationKind a owl:Class .
    bro:EnumerationKind-Operating_Mode a owl:Class ; rdfs:subClassOf bro:EnumerationKind .
    bro:EnumerationKind-Occupancy a owl:Class ; rdfs:subClassOf bro:EnumerationKind .

    bro:hasEnumKind a owl:ObjectProperty ;
        rdfs:domain qudt:QuantityValue ; rdfs:range owl:Class .

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
        qudt:quantityValue ex:controllerDefaultModeValue .

    ex:controllerDefaultModeValue
        a qudt:QuantityValue ;
        bro:hasEnumKind bro:EnumerationKind-Operating_Mode ;
        qudt:value bro:Occupancy-Occupied .

### 3.5 Enumerated Value with a Timestamp

    ex:room101
        a bro:Space ;
        bro:hasProperty ex:roomOccupancy .

    ex:roomOccupancy
        a bro:Occupancy_Sensor ;
        qudt:quantityValue ex:roomOccupancyReading1200 ;
        bro:lastKnownValue ex:roomOccupancyReading1200 .

    ex:roomOccupancyReading1200
        a qudt:QuantityValue ;
        bro:hasEnumKind bro:EnumerationKind-Occupancy ;
        qudt:value bro:Occupancy-Occupied ;
        bro:timestamp "2026-11-13T12:00:00Z"^^xsd:dateTime .

### 3.6 Connecting a Property to a Point

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

#.  TODO: If several points provide values for the same property, how do we know which point a last-known value came from?

#.  TODO: If we can read a point through multiple external references (e.g., BACnet or a historian), how do we say where a value came from?

##  4. Property Semantics

#.  The attributes associated with Points in Brick 1.x are now going on the Property. The Point carries the access information and its own attributes (§5.2).

#.  Some examples: Temperature goes in `qudt:hasQuantityKind`; Air goes in `bro:ofMedium`. We use `bro:ofConstituent` for ammonia in air, `bro:inMode` for Occupied, `bro:measurementModifier` for Average, and `bro:hasPhenomenon` for Solar. The enum kind goes on the value record (§3.4).
#.  We also propose Phenomenon as a supertype of Substance; see [§4.5 below](#phenomenon).

#.  We use the enum pattern from §3.4: each named value is a class and an instance of itself. Occupied and Unoccupied sit under both Operating_Mode and Occupancy. Names include their parent kind, e.g., `Occupancy-Unoccupied`.
#. TODO: how do we provide the mapping between the enums and the actual data values? For example, Occupancy-Unoccupied might be 0 in one system and 1 in another. Borrow from KNX?

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

#.  The property can say Occupied, Average, or Solar. Its value record says which enum it uses. 

### 4.1 Quantity Kind and Medium

#.  This is the room's air temperature. Temperature is the quantity kind; Air is the medium. Both go on the property.

    ex:room101
        a bro:Space ;
        bro:hasProperty ex:room101AirTemperature .

    ex:room101AirTemperature
        a bro:Room_Air_Temperature_Sensor ;
        bro:name "Room 101 representative air temperature"@en ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air .

### 4.2 Constituent and Medium

#.  Here we're measuring ammonia in air. Ammonia is the constituent; Air is the medium. We use the [223 constituent/medium pattern](https://docs.open223.info/medium-mixtures/).

    ex:room101 bro:hasProperty ex:ammoniaFraction .

    ex:ammoniaFraction
        a bro:Ammonia_Sensor ;
        qudt:hasQuantityKind qk:AmountOfSubstanceFraction ;
        bro:ofConstituent bro:NH3 ;
        bro:ofMedium bro:Air ;
        bro:description "Ammonia mole fraction in air."@en .

#.  This value is a mole fraction. If we report mass per volume or a volume fraction instead, we need a different quantity kind. It's still ammonia in air.

### 4.3 Operating Mode

    bro:Operating_Mode-LoadShed
        a owl:Class, bro:Operating_Mode-LoadShed ;
        rdfs:subClassOf bro:EnumerationKind-Operating_Mode .

#.  `inMode` says when a setpoint applies. An occupied cooling setpoint applies in Occupied mode; that doesn't mean the room is currently occupied.
#. Importantly, if you use the same enum values for the setpoint's mode and the reported mode, then you can compare them directly to see which setpoints apply at a point in time.


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
        bro:lastKnownValue [
            a qudt:QuantityValue ;
            bro:hasEnumKind bro:EnumerationKind-Operating_Mode ;
            qudt:value bro:Occupancy-Occupied ;
            bro:timestamp "2026-11-13T12:00:00Z"^^xsd:dateTime
        ] .

#.  TODO: Can Occupied and LoadShed apply at the same time? How do we combine modes or pick which setpoint applies?
#.  I think it's important to not try to model all of the fallback logic redundantly inside the knowledge graph. But what is a useful thing for us to model instead? 

### 4.4 Measurement Modifier

#.  TODO: Average over what time period? Maximum or minimum of which readings? We need to say what we're comparing. Consider having properties for the aggregation parameters.

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

### 4.5 Phenomenon <a id="phenomenon"></a>

#.  We propose Phenomenon as a supertype of Substance.

#.  Solar, Hail, Frost, Contact, and People come from our notes. Solar azimuth has a numeric value; door contact has an enum value. Saying what we're observing doesn't tell us the value's unit or datatype.
#. This phenomenon stuff is similar to what Haystack does.

    bro:Phenomenon-Solar
        a owl:Class, bro:Phenomenon-Solar ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .
    bro:Phenomenon-Hail
        a owl:Class, bro:Phenomenon-Hail ;
        rdfs:subClassOf bro:EnumerationKind-Phenomenon .
    bro:Phenomenon-Frost
        a owl:Class, bro:Phenomenon-Frost ;
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
        bro:lastKnownValue [
            a qudt:QuantityValue ;
            bro:hasEnumKind bro:EnumerationKind-Open_Close ;
            qudt:value bro:Open_Close-Closed ;
            bro:timestamp "2026-11-13T12:00:00Z"^^xsd:dateTime
        ] .

    ex:room101 bro:hasProperty ex:roomOccupancy .

    ex:roomOccupancy
        a bro:Occupancy_Sensor ;
        bro:hasPhenomenon bro:Phenomenon-People ;
        qudt:quantityValue ex:roomOccupancyReading1200 ;
        bro:lastKnownValue ex:roomOccupancyReading1200 .

### 4.6 Differences

#. TODO: Do we need to go as deep as 223, which has the measurement positions A and B? 

    ex:filter1 a bro:Filter ; bro:hasProperty ex:airPressureDifference .

    ex:airPressureDifference
        a bro:Air_Differential_Pressure_Sensor ;
        bro:ofMedium bro:Air ;
        qudt:hasQuantityKind qk:Pressure ;
        qudt:isDeltaQuantity true ;
        bro:description "Upstream air pressure minus downstream air pressure across filter 1."@en .

#.  We use [QUDT's `isDeltaQuantity`](https://qudt.org/schema/qudt/isDeltaQuantity) because this is a pressure difference. That goes on the property, whichever unit the point reports.

##  5. Points and Access

### 5.1 External Access through a Point

#. A `Point` represents the digital object that exposes a property's value. It can be hosted by a controller; it can have a network address, a historian ID, or other access information.
#. It can have its own attributes, such as resolution or accuracy. A point can be associated with a physical sensor or actuator, but it is not the sensor itself (that is `bro:SensorEquipment` or `bro:ActuatorEquipment`).
#. A point can be associated with a property, but it is not the property itself (that is `bro:Property`).
#. There are no subclasse of `Point` yet.
#. TODO: what would we reserve subclasses for? By protocol, e.g. BACnet point?

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

#. A `Point` can have its own properties, such as minimum, maximum, and resolution. We use `bro:hasProperty` for these, just as we do for properties of equipment or spaces.
#. The point provides a value for the room's temperature property (`bro:providesValueFor`).

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

#. The minimum and maximum describe the range of values the point exposes; they are not the room's operating limits. Resolution describes the smallest increment the point can report (0.1 °C here), not the physical sensor's accuracy. It is a difference, so we use `qudt:isDeltaQuantity`.
#. A property's value can be given directly, be unknown, or come from another point. For example, if we can read the resolution from the controller, we can describe that access with another point that provides a value for the resolution property.

### 5.3 Multiple Access Systems and Readers

#. What if a Point is available through BACnet, but we have the data stored in a historian, too? Because the Point is the digital object that exposes the value, we can have multiple access paths to it. Each access path can have its own address or ID.

    ex:temperatureA
        a bro:Point ;
        bro:pointName "R101_TEMP_A" ;
        bro:providesValueFor ex:room101AirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        # here's the BACnet access path
        bro:hasAddress "bacnet://123/analog-input,3/present-value" ;
        # here's the historian access path
        bro:storedAt ex:historian ;
        bro:hasTimeseriesId "room101-temperature-a" .

#.  TODO: It feels a little awkward to have the two access paths on the same point. Should the two access paths be separate points?

### 5.4 Independent Points for One Property

#.  Two independent sources (like 2 different sensors) can provide values for the same identified property.
#.  Each point declares its own unit and datatype, which may be different. The property declares the quantity kind, which is the same for both points.

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
        bro:hostsPoint ex:temperatureA .

    ex:sensorEquipmentASerialNumber
        a bro:SerialNumberProperty ;
        qudt:value "PROBE-A-772" .

    ex:sensorEquipmentB
        a bro:SensorEquipment ;
        bro:hasProperty ex:sensorEquipmentBSerialNumber ;
        bro:hasLocation ex:room101 ;
        bro:hostsPoint ex:temperatureB .

    ex:sensorEquipmentBSerialNumber
        a bro:SerialNumberProperty ;
        qudt:value "PROBE-B-884" .

### 5.5 Internal References

#. A common modeling problem we had before was a single outside air temperature sensor which was re-exposed virtually at many places in a BACnet network.
#. We have one sensor, but three points with different addresses. They all provide values for the same outside-air-temperature property. How do we say that two of the points are copies of the first?
#. We propose `bro:hasInternalReference` to point from a virtual point to the point it copies. The network address goes on each point, as before.

    bro:hasInternalReference
        a owl:ObjectProperty ;
        rdfs:domain bro:Point ;
        rdfs:range bro:Point .

    ex:site1
        a bro:Site ;
        bro:hasProperty ex:outsideAirTemperature .

    ex:outsideAirTemperature
        a bro:Outside_Air_Temperature_Sensor ;
        qudt:hasQuantityKind qk:Temperature ;
        bro:ofMedium bro:Air .

    ex:outsideAirSensor
        a bro:SensorEquipment ;
        bro:hasLocation ex:site1 ;
        bro:hostsPoint ex:outsideAirTemperaturePoint .

    ex:outsideAirTemperaturePoint
        a bro:Point ;
        bro:providesValueFor ex:outsideAirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://101/analog-input,1/present-value" .

    ex:virtualOutsideAirTemperatureA
        a bro:Point ;
        bro:providesValueFor ex:outsideAirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://201/analog-value,10/present-value" ;
        bro:hasInternalReference ex:outsideAirTemperaturePoint .

    ex:virtualOutsideAirTemperatureB
        a bro:Point ;
        # this providesValuesFor could be inferred from the internal reference (see below)
        bro:providesValueFor ex:outsideAirTemperature ;
        bro:providesValueOfType xsd:decimal ;
        bro:hasUnit unit:DEG_C ;
        bro:hasAddress "bacnet://202/analog-value,15/present-value" ;
        bro:hasInternalReference ex:outsideAirTemperaturePoint .

    ex:sourceController
        a bro:PhysicalEquipment ;
        bro:hostsPoint ex:outsideAirTemperaturePoint .

    ex:virtualControllerA
        a bro:PhysicalEquipment ;
        bro:hostsPoint ex:virtualOutsideAirTemperatureA .

    ex:virtualControllerB
        a bro:PhysicalEquipment ;
        bro:hostsPoint ex:virtualOutsideAirTemperatureB .

#. We keep each point's address and host because the copies are still separate digital objects.
#. Following `bro:hasInternalReference` takes us back to the original point and its physical sensor.
#. It feels a little awkward that we need to include both `hasInternalReference` and `providesValueFor`, but we can infer the `providesValueFor` edge by following the internal reference.

    bro:InferPointReferenceShape a sh:NodeShape ;
        sh:targetClass bro:Point ;
        sh:targetSubjectsOf bro:hasInternalReference ;
        sh:rule [
            a sh:TripleRule ;
            sh:subject sh:this ;
            sh:predicate bro:providesValueFor ;
            sh:object [
                sh:path ( bro:hasInternalReference bro:providesValueFor )
            ]
        ] .
