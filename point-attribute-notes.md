Quantity Kind:
- Absolute Humidity
- Active Power
- Concentration? need to distinguish the QKs: ammonia, radon, co2
	- radon: `ActivityConcentration`
		- add piC/L to QUDTQK for radon?
	- others (from 223): possible `QuantityKind`s include `Concentration` (moles per volume), `Density` (mass per volume), `MoleFraction`, and `VolumeFraction`
- Azimuth (Angle)
	- Solar Azimuth ; azimuth w.r.t. Sol
- Zenith (Angle) -- missing in QUDT?
	- Solar Zenith 
- wet bulb temperature
- electricity demand (subtype of power)
- capacity
- core temperature
	- of a radiant slab
- current
- conductivity
- correlated color temperature
- count
- TODO:
	- ask Steve Ray about Grains (for air grains of moisure)
	- use the brick quantities.py to update the Points table with the right quantity kinds
		- use quantitykind:AmountOfSubstanceFraction instead of the older QK:DimensionlessRatio for our air quality quantity kinds

Equipment (ofEquipment):
- Battery

Power/Energy "direction"? (could help with normalizing some other point names)
- production
- consumption
- demand

Substance:
- Ammonia
- Waste / trash
- Building Air
- Bypass Air
- Bypass water
- collection basin water
- CO2
- CO
- chilled water
- Condenser water
- Light
	- for "Lighting (Light) Correlated Color TEmperature sensor"
(revise this list in brick 2.0; )

Phenomenon: (for brick 2.0; supertype of substance)
- Solar
- Hail
- Frost
- Noise
- Contact
- people (occupancy)

Medium:
- Air

Measurement Modifier:
- Average
- Maximum
- Minimum
- Coldest
- Warmest
- Peak
- ~~not Absolute; we only use this in combination with Absolute Humidity, so we are not distinguishing this from the mathematical notion of Absolute~~

New dimension: "building mode/control mode"
- Occupied
- Unoccupied
- Load Shed
- *these capture the "mode" we're operating under, enabling selection of which setpoints/statuses are active*

One-off points that don't have annotations on all their parts:
- brick:Change_Filter_Alarm 

Possible deprecations, due to unclear definitions/quantity kinds/and use cases:
	- waste amount sensor (clarify as fill level sensor)
	- warm/cool temperature adjust sensor
	- DC bus voltage sensor
	- RENAME "peak demand sensor" to "peak electricity demand sensor"?
	- air flow demand setpoint -> air flow setpoint
		- explain how this is different! Else we will deprecate
	- box mode command (what is this? could not find a definition)
	- bypass command
		- too much variation in the definition (is it a ratio of face/bypass damper positions? position of just the bypass damper?)
		- need guidance on how modeling face/bypass damper system points can be improved
	- brick:Close_Limit (says it limits the values of a close setpoint, but there is no close setpoint)

2.0 changes:
- "DC Bus" is a connection, not an equip
	- include from 223p?
- unify/simplify CO_Sensor and CO_Level_Sensor (and likewise for other air quality points)
	- we introduced CO_Level_Sensor to be more specific, but we haven't needed to differentiate CO_Sensor any further. Do we need the subclass?

Questions to Brick community + consortium:
- how to model quantity/substance of radiant panel temperature sensors
	- is substance "concrete"?
	- how to break out the "measurement location", i.e. core, outside surface face, inside surface face
- PID loop points. How do we want to model these?
	- ask Brick OR 231P folks
	- acceleration time setpoint
- introducing "abstract classes" whic hgroup, but cannot be instantiated
	- brick:Air_Quality_Sensor 
	- brick:Boiler_Command
		- these don't have any meaning on their own. Could be an enable_cmd, run_cmd, etc
		- if abstract, then add boiler_enable_cmd, boiler_run_cmd
	- *if there are no other examples, then maybe we either deprecate air quality sensor, or we find another way to define it. It is a useful umbrella term for many sensor classes*
- Alarm parameters; how to model?
	- brick:Alarm_Delay_Parameter
	- brick:Alarm_Sensitivity_Parameter
- EnumerationKinds for commands to capture specific annotations:
	- ```
	  brick:Automatic_Mode_Command
	  brick:hasEnum? s223:EnumerationKind-Binary -> brick:EnumerationKind-AutomaticMode = {brick:Automatic-Mode-True, brick:Automatic-Mode-False}
	  ```
- how to break "cooling" and "heating" out into annotations on classes:
	- are they a process? system? equipment? what's the relationship between "brick:Average_Cooling_Demand_Sensor" and Cooling?
- do we need a "thermal demand" to capture cooling/heating demand as a subtype of power?
	- cooling demand (subtype of power)
		- "power required to meet the cooling needs"?
	- heating demand (subtype of energy)
- CO2 Alarm and related Alarms:
	- do we need more specific subclasses? Or is it always clear what a CO2 Alarm is?

Aliases:
- brick:Air_Velocity_Pressure_Sensor -> air_dynamic_pressure_sensor

Alarm:
- need to change the semantics of how hasQuantity is used. Alarm units wil lbe different (probably binary/boolean, or 3-valued bool), but we still want the annotations of what quantitykind the alarm is about.

---

Ammonia Sensor
	- hasSubstance: NH3
	- hasMedium: Air
