# NyanSat
## Combined Payload Board

The Combined Payload Board (CPB) is a component of the NyanSat project, designed to interface with a variety of payloads, including imaging, material science experiments, the Attitude Determination and Control Subsystem (ADACS), and the acoustic spacecraft mapping subsystem.

### Internal Interfaces
* Mechanical: PC/104 form factor for holes and board outline.
    * SMT components all on bottom side of board; top side for connectors and stacking Raspberry Pi Zero 2W.
* 40 pin spacecraft electrical bus
	* Battery voltage (post-inhibit switches, current sensing, current limiting on OBC).
	* One GPIO: Gating of battery voltage to board except bridge driver
    * One GPIO: Gating of battery voltage to bridge driver
	* I2C
		* I2C-UART Address: `0x90 = 1001 0000`
            * Raspberry Pi: controlled by serial from I2C-UART.
            * Pi power controlled by I2C-UART GPIO
            * 3 external power switches controlled by I2C-UART GPIOs.
        * I2C-SPI converter for driver bridges `0x50 = 0101 000X`
            * MAX22200 bridge chip select, command select, and enable controlled by I2C-SPI converter.
		* Microcontroller Address: TBD
	* Ground/return path for power and all signals
* Mounting holes
	* 0603 pads from holes to board ground exist; can populate with capacitor (AC-couple to stack), resistor, short, or leave open.

### External Interfaces
* 4 versatile analog inputs. provided by microcontroller ADC, 0-3.3V 12 bit 1.1MSPS.  Pads on board allow selections of various modes:
	* Divided voltage or pulled towards chosen voltage
	* Resistive termination or known impedance
	* AC coupling
    * Pin alternate functions allow use for digital inputs or timer outputs
* 2 DC (0-5V) or AC-coupled waveform generation channels produced from microcontroller DAC with ~40mA drive capability
	* Controlled by microcontroller; feedback to ADC allows precise control of voltages.
	* Low pass filter: 2nd order with -3dB bandwidth of 100KHz.
* 8 high-side switches of battery voltage for payloads and magnetorquers
	* 3 controlled by GPIO from I2C-UART peripheral
	* 1 controlled by onboard Raspberry Pi computer
	* 4 controlled by onboard Cortex-M0+ microcontroller
* 8 half-bridges with programmable current limits
* 5 pin connection for external I2C peripheral (e.g. magnetometer)
* On Pi: CSI interface for Raspberry Pi camera

### Anticipated Use
* Attitude Determination and Control Subsystem (ADACS):
	* Switches: 3 magnetorquers, bipolar (6 half-bridges)
* Acoustic Spacecraft Sounding:
	* Switch: Solenoid, driven by pair of half-bridges
	* Possible switch: MEMS microphone power
	* ADC: 2 channels of MEMS microphone.
* Material science / semiconductor characterization experiment  
	* Waveform generation: 2 channels of drive voltages.
	* ADC: 2 channels of measurement.
* Camera
	* Pi/CSI to camera.
	* Possible high powered LED via high side driver.
* Deployables
	* Option open to use high-side switches channels from here for deployment.
	* Consider using this board to energize second deployable resistor for redundancy.

### Stored-Program Computing Components
* Microcontroller; used mostly as a smart ADC/DAC.
	* STM32L072RBT6; 32MHz Cortex-M0+ with single-cycle multiplier, 128k flash, 6k data EEPROM, 20k RAM.
* Raspberry Pi Zero 2W; used mostly as interface for camera.
	* Quad-core Cortex A53 64-bit processor, 512MB SDRAM.
	* Wireless capabilities (disabled in flight) can be used for ground troubleshooting.
	* Use SLC microSD card, e.g. SFSD2048N1BN1WI-E-QF-111-STD from SwissBit.

### Design Philosophy
* Board's power is gated and will be completely de-powered if a pin on spacecraft electrical bus is not driven high.
* Seperate power gate for magnetorquer full-bridge.
* No high-level sequencing of experiments performed by this board; enabled and commanded from main On-Board Computer (OBC).
* I2C is isolated from spacecraft bus if de-powered or peripheral locks up.
* Large portions of functionality work even if non-volatile memory on a device is corrupted.
	* I2C-UART/GPIO chip does not have nonvolatile memory, and directly controls 4 output switches.
    * I2C-SPI and MAX22200 do not have nonvolatile state.
	* Microcontroller starts up held in reset until explicitly enabled by I2C-UART.
	* If microcontroller is corrupted, Raspberry Pi is capable of reprogramming via on-chip debug features.
    * Pi's power supply is separately enabled by I2C-UART.
	* If Pi filesystem is corrupted, small chance of recovery through failsafe pin (indicates to boot from different part of SD card).

### Known Weaknesses & Shortcomings
* No provision to measure power used by this board.
* No real current limiting beyond master current limiting from ADM1176 PMIC on OBC.
	* But: If entire satellite resets due to overcurrent, this board will revert to default state (turned off).
    * 200 mohm resistors and FET Rds do provide a little bit of mitigation of catastrophes.
* Raspberry Pi must be turned on to generate waveforms / run matsci experiment due to board architecture (needs 5V rail).
* Only having 4 ADC is a little limiting.
