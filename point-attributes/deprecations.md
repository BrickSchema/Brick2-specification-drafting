Possible deprecations, due to unclear definitions/quantity kinds/and use cases:
- waste amount sensor (clarify as fill level sensor)
- warm/cool temperature adjust sensor
- `brick:DC_Bus_Voltage_Sensor`:
    - make `DC Bus` a new equipment?
- RENAME "peak demand sensor" to "peak electricity demand sensor"?
- air flow demand setpoint -> air flow setpoint
	- explain how this is different! Else we will deprecate
- box mode command (what is this? could not find a definition)
- bypass command
	- too much variation in the definition (is it a ratio of face/bypass damper positions? position of just the bypass damper?)
	- need guidance on how modeling face/bypass damper system points can be improved
- brick:Close_Limit (says it limits the values of a close setpoint, but there is no close setpoint)
- for the "level" sensors (e.g. `CO_Level_Sensor`) we decided to deprecate the "generic" versions (i.e. `CO_Sensor`) since we aren't using them as parents of any other classes. We can always re-add
- `Contact_Sensor`: Deprecate: instead model open/close status on door or window or equipment?
- `air_grains_sensor`: deprecate until we fix the definition of GRAIN
- `brick:Coldest_Zone_Air_Temperature_Sensor`: deprecate; replace with "lowest"
- `brick:Dehumidify_Command` -> rename to `Dehumidification_Command`
- `brick:Differential_Entering_Leaving_Water_Temperature_Sensor`; easier to model in 223

- these seem like they are related to "location"; deprecating for now
    - `current_output_sensor`
    - `output_voltage_sensor`
    - `output_frequency_sensor`


- `brick:Curtailment_Override_Command`
