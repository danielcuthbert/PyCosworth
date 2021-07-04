# Lancia Delta HF Turbo

The Lancia Delta HF Turbo uses the Weber Marelli IAW 049/01 ECU

## Communicating with the ECU

The ECU makes use of a 3-pin diagnostics cable, so you will need that, which can be found on eBay with a 16-pin OBDII connector and then you'll need power via crocodile clips, or a hacked iPhone usb charging cable, this is due to the ECU not providing power like OBDII does. 

It should look like this

`integrale to FIAT 3pin / OBDII to OBDII / USB to laptop`

It is RS232 and the port settings you'll need are:

`Baud rate: 1952`  
`Byte size: 8`  
`Parity: 'N'`  
`Stop bits: 1`  
`XON/XOFF: Off`  
`RTS/CTS: Off`  
`DSR/DTR: On`

Thankfully there is no initialisation sequence, so once power is applied to the ECU, serial comms are active and you can start reading and writing data. In order to read sensor information, you need to send a single byte and read a single byte back again. 


## What can be read?

You can get a fair amount of data from the ECU about components, bear in mind this is not as advanced as modern ECUs but there is enough there for diagnostics and fault finding. 

### Running Parameters

* RPM (crank sensor),
* intake manifold pressure (MAP),
* secondary intake manifold pressure (second MAP, where available)
* throttle position (TPS),
* CO trim pot position,
* coolant temperature (CTS),
* air temperature (ATS),
* battery voltage (ECU pin 20),
* injector pulsewidth,
* lambda fueling correction integrator,
* injection timing,
* ignition advance,
* boost control valve duty cycle,
* idle air control valve duty cycle,
* error code numbers.

### Error and status codes
You can read some error messages and status codes (not reliable)

* Crank trigger resistance,
* crank - distributor trigger signal panel,
* camshaft trigger resistance,
* camshaft trigger signal,
* manifold pressure sensor (MAP),
* throttle position sensor (TPS),
* air temperature sensor (ATS),
* coolant temperature sensor (CTS),
* knock sensor (where available),
* lambda sensor (where available),
* engine load state,


### Actuator tests
The following tests can be performed

* Fuel pump,
* injectors in sequence,
* ignition,
* idle valve,
* boost control valve.


## Byte Codes

Not complete at all but a start, this will be updated as/when possible. 

`RPM (most significant byte), 0x01, 30000000 / DATUM = RPM`
`RPM (lest significant byte), 0x02, 30000000 / DATUM = RPM`
`Injection duration (msb), 0x03, 4 * DATUM / 10^3 = milliseconds`
`Injection duration (msb), 0x04, 4 * DATUM / 10^3 = milliseconds`
`Ignition advance, 0x05, DATUM 1L/4 = degrees`
`Intake pressure, 0x06, DATUM * 6.4161 + 45.63 = mmHg`
`Air temp, 0x07`
`Water temp, 0x08`
`TPS, 0x09, if < 30 (DATUM * 0.1848) - 1.41 = Degrees, if >= 30 (DATUM * 0.7058) - 90 = degrees`
`Voltage, 0x0A, DATUM * 0.0628 = volts`
`Fuel trimmer, 0x0B, DATUM - 128`
`Injection timing angle, 0x0C, 720 - (DATUM * 90) / 4 = Degrees`

Most sensors need two reads (lower 8 bits and upper 8 bits) to get the data and then you'll need to convert them to be of any use to humans. 
