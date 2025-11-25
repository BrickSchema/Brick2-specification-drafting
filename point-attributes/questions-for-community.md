Questions to Brick community + consortium:
- how to model quantity/substance of radiant panel temperature sensors
	- is substance "concrete"?
	- how to break out the "measurement location", i.e. core, outside surface face, inside surface face
- PID loop points. How do we want to model these?
	- ask Brick OR 231P folks
	- acceleration time setpoint
- introducing "abstract classes" whic hgroup, but cannot be instantiated
	- `brick:Air_Quality_Sensor`
	- `brick:Boiler_Command`
		- these don't have any meaning on their own. Could be an enable_cmd, run_cmd, etc
		- if abstract, then add boiler_enable_cmd, boiler_run_cmd
	- *if there are no other examples, then maybe we either deprecate air quality sensor, or we find another way to define it. It is a useful umbrella term for many sensor classes*
- Alarm parameters; how to model?
    - `brick:Alarm_Delay_Parameter`
    - `brick:Alarm_Sensitivity_Parameter`
- EnumerationKinds for commands to capture specific annotations:
    - `brick:Automatic_Mode_Command`
    - `brick:hasEnum? s223:EnumerationKind-Binary -> brick:EnumerationKind-AutomaticMode = {brick:Automatic-Mode-True, brick:Automatic-Mode-False}`
- how to break "cooling" and "heating" out into annotations on classes:
	- are they a process? system? equipment? what's the relationship between "brick:Average_Cooling_Demand_Sensor" and Cooling?
- do we need a "thermal demand" to capture cooling/heating demand as a subtype of power?
	- cooling demand (subtype of power)
		- "power required to meet the cooling needs"?
	- heating demand (subtype of energy)
- CO2 Alarm and related Alarms:
	- do we need more specific subclasses? Or is it always clear what a CO2 Alarm is?
- Alarms:
    - always boolean/3-valued? Need to model the different "statuses" of alarms? how do these relate to enum kinds for statuses

- Grains:
    - https://www.energyvanguard.com/blog/building-science-term-of-the-week-grains-of-water/
    - *grains* are just mass (this is for qudt GRAIN unit), but actually what we care about is the grains of water vapor per pound of dry air.
    - we might need to change QUDT unit and Brick both to model this accurately

- Radiant panels:
    - how do you want to model the positions of different sensors?
        - core
        - embedded
        - inside face surface
        - outside face surface
        - others?
    - are core/embedded the same? what's the range of values? is this a "location" or something else?

- electrical points:
    - current imbalance:
        - ask steve ray how to model in QUDT
        - michael poplawski might know too
    - current and voltage ratio (for transformers)
    - `brick:DC_Bus_Voltage_Sensor`: 
        - should DC Bus be a new equipment? or is it a connection?
        - ask Michael Poplawski


- enthalpy controls (for economizers?):
    - `brick:Enable/Disable_Differential/Fixed_Enthalpy_Command`
    - what's the right way to break this down? is "differential/fixed enthalpy" a process?
- similar: fixed/differential temperature (pretty sure we meant "dry bulb temperature" here)
- https://alpineintel.com/resource/economizer-basics-who-uses-them-where-theyre-required-and-why-they-wind-up-in-claims/
