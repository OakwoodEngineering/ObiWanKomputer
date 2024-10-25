# NyanSat
## Combined Payload Board

The Combined Payload Board (CPB) is part of the NyanSat project.  Its purpose is to provide interfaces for various payloads (imaging, material science experiment, ADACS, acoustic spacecraft mapping).

### Internal Interfaces
* Mechanical: PC/104 form factor for holes and board outline.
* 40 pin spacecraft electrical bus
	* Battery voltage (post-inhibit switches, current sensing, current limiting on OBC).
	* One GPIO: Gating of battery voltage to board
	* I2C
		* I2C-UART Address: `0x90 = 1001 0000`
		* Microcontroller Address: TBD
		* Raspberry Pi: controlled by serial from I2C-UART.
	* Ground/return path for power and all signals
* Mounting holes
	* 0603 pads to board ground exist; can populate with capacitor (AC-couple to stack), resistor, short, or leave open.

### External Interfaces
* 4 versatile analog inputs. provided by microcontroller ADC, 0-3.3V 12 bit 1.1MSPS.  Pads on board allow selections of various modes:
	* Divided voltage or pulled towards chosen voltage
	* Resistive termination or known impedance
	* AC coupling
* 2 DC (0-5V) or AC-coupled waveform generation channels produced from microcontroller DAC with ~40mA drive capability
	* Controlled by microcontroller; feedback to ADC allows precise control of voltages.
	* Low pass filter: 2nd order with -3dB bandwidth of 100KHz.
* 8 high-side switches of battery voltage for payloads and magnetorquers
	* 4 controlled by GPIO from I2C-UART peripheral
	* 1 controlled by onboard Raspberry Pi computer
	* 3 controlled by onboard Cortex-M0+ microcontroller
* CSI interface for Raspberry Pi camera

### Anticipated Use
* Attitude Determination and Control Subsystem (ADACS):
	* Switches: 3 magnetorquers.
* Acoustic Spacecraft Sounding:
	* Switch: Solenoid.
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
* No high-level sequencing of experiments performed by this board; enabled and commanded from main On-Board Computer (OBC).
* I2C is isolated from spacecraft bus if de-powered or peripheral locks up.
* Large portions of functionality work even if non-volatile memory on a device is corrupted.
	* I2C-UART/GPIO chip does not have nonvolatile memory, and directly controls 4 output switches, along with independent enabling of Raspberry Pi and microcontroller.
	* Microcontroller starts up held in reset until explicitly enabled by I2C-UART.
	* If microcontroller is corrupted, Raspberry Pi is capable of reprogramming via on-chip debug features.
	* If Pi filesystem is corrupted, small chance of recovery through failsafe pin (indicates to boot from different part of SD card).

### Known Weaknesses & Shortcomings
* No provision to measure power used by this board.
* No current limiting beyond master current limiting from ADM1176 PMIC on OBC.
	* But: If entire satellite resets due to overcurrent, this board will revert to default state (turned off).
* Raspberry Pi must be turned on to generate waveforms / run matsci experiment due to board architecture (needs 5V rail).
* Only having 4 ADC is a little limiting.
