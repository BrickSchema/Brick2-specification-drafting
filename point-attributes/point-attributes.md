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
- Imbalance for current/voltage
    - put into QUDT? percentage deviation from average current/voltage

Equipment (ofEquipment):
- Battery

Process:
- Cooling
- Heating
- Ventilation?

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
- Condensate (for leaks) -- same as condenser water?
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

2.0 changes:
- "DC Bus" is a connection, not an equip
	- include from 223p?
- unify/simplify CO_Sensor and CO_Level_Sensor (and likewise for other air quality points)
	- we introduced CO_Level_Sensor to be more specific, but we haven't needed to differentiate CO_Sensor any further. Do we need the subclass?


Aliases:
- brick:Air_Velocity_Pressure_Sensor -> air_dynamic_pressure_sensor

Alarm:
- need to change the semantics of how hasQuantity is used. Alarm units wil lbe different (probably binary/boolean, or 3-valued bool), but we still want the annotations of what quantitykind the alarm is about.

Alarm Units:
- alarms have normal/abnormal "states"
    - **Question: are alarms always binary?**
    - *probably* helpful in the alarm enumerations to distinguish which states are normal vs abnormal
    - we might be able to combine this with enumeratiosn for brick *Status* points, which don't care about
      normal/abnormal, but will enumerate the same states
- `Communication_Loss_Alarm`:
    - hasEnumeration: `Communication_Status` = {`Communication_Status-Lost`, `Communication_Status-Established`}

Commands:
- `Cooling_Command`: Cooling is a "process" that is being affected 
    - want to capture that it is affecting a cooling system or process
    - do we have a "ofProcess" to complement "ofEquipment?"

---

Ammonia Sensor
- hasSubstance: NH3
- hasMedium: Air
