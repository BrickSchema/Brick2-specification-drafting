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
    - add ElectricEnergy to QUDT as a quantity kind? (steve ray)
        - similar to ElectricPower, which *does* exist in QUDT
        - also: Thermal Power? We have thermal energy but not thermal power
- reactive energy -- is this real? kvarh would be the unit we see
    - ask steve ray
- related: generation/load
    - for generation/load points do we want to capture generation/load as properties (like a "process") or are these captured by connection points, or something else?
    - how to handle `brick:Natural_Gas_Usage_Sensor`; we will probably model with Volume, but how to handle points that monitor *production* of gas, e.g. hydrogen
- related: `brick:Generation_Sensor` which is not electricity-specific
- related: load shedding process and related points:
    - max load setpoint, min load setpoint
- `brick:Photovoltaic_Current_Output_Sensor` deprecated:
    - alternative `Current_Generation_Sensor` on a PV panel or system?


- enthalpy controls (for economizers?):
    - `brick:Enable/Disable_Differential/Fixed_Enthalpy_Command`
    - what's the right way to break this down? is "differential/fixed enthalpy" a process?
- similar: fixed/differential temperature (pretty sure we meant "dry bulb temperature" here)
- https://alpineintel.com/resource/economizer-basics-who-uses-them-where-theyre-required-and-why-they-wind-up-in-claims/

- lead lag controls:
    - command/status. ARe there standard/common enumerations that we should model?
    - `brick:Lead_Lag_Command`
    - `brick:Lead_Lag_Status`
    - `brick:Lead_On_Off_Command`

- compressor points:
    - high head pressure alarm
    - low suction pressure alarm
    - *how to model these? is head pressure a type of pressure? is suction pressure a type of pressure?*
    - can we get away with just high/low pressure alarms on the compressor?

- runtime / uptime / "on" timer
    - is it worth differentiating between these? A pump could be "on" but not "Running", or likewise "enabled" but not actively pumping
    - or do we just have a "runtime" sensor?
    - deprecating for now

- brick:Outside_Illuminance_Sensor
    - ask michael poplawski if there's a better name we can use
    - do we need to model the archtiectural spaces? would this be used for daylighting?
    - or is to too niche and we just deprecate?
    - see also: deprecations.md

- brick:Run_Request_Status: what are the enumeration, possible values here? Other variations of "request" other than requested/unrequested

- HVAC stages (`brick:EnumerationKind-Stage (need community values)`)
    - 1/2 stage heat/cool? others?

- How should we add "standby" as a concept? is it orthogonal to on/off, enabled/disabled, running/not running?
    - is standby a mode? or a status? or something else?
