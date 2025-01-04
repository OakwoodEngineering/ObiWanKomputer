- Revision 1
    - From PyCubed, CRITICAL: high energy protons cause single-event latchup.  see: https://github.com/pycubed/forums/discussions/87
       > We have found the ATSAMD51 microcontroller used on PyCubed Mainboards (v00 - v05) exhibiting a surprisingly high sensitivity to 50MeV protons. Our KickSat-2 and VR3X missions had zero issues with the ATSAMD51 and the part does have some radiation literature. But after troubleshooting unexplained reboots on our recent PY4 mission, we decided to test it ourselves.
    - Thermal pad for radio is in incorrect mechanical position (mirror image?)
    - Solar charger seems to hit switch current limit when asked to provide 400mA charge to battery.  Need a better inductor (and/or higher switch frequency), and also should choose a current limit with more margin for ripple-- 300-350mA.
    - Have not been able to make GPS work.  GPS enable circuit and UART work, but no satellite lock was achieved.
    - Was not able to plug male connectors into MMCXV high-rel RF connectors.  Possble we did not have the correct mating connector, or possible insertion forces are implausibly high.
    - Connector labeling is insufficient.  Need polarity marking and connector purposes.
    - Board coordinate system is probably not correct wrt: satellite coordinate system.
    - U103 pin marking isn't visible with installed part / is under body
    - Too many components require manual rotation during JLCPCB ordering
    - Routing for CHRG# net is pretty terrible
    - Flash chips are not connected to a valid SPI bus on the microcontroller.
    - Our solar arrays could potentially create more than 400mA charge currents, and hit the current limit of the LTC4121.  Consider adding another '4121.
        - The inductor is also oversized, being rated for 1.2A avg current.
    - From JLCPCB DFM review:
        - L101 doesn't fit; substituting 0806 inductor, DFE201610E-100M=P2
          C426327 for order
        - D501 is a pseudo SOD-123 in a power-optimized package but we have
          ordinary SOD-123 pads; substituting 1N5819HW-7-F p/n C82544 for
          order
    - MAX706RESA watchdog timer producing incorrect/unexpected results: the RESET# pin would produce a reset pulse correctly upon powerup when power was toggled (as datasheet indicated), but would then get stuck in a reset loop (RESET# held low) when WDI was held low after the first cycle.
    - No pin 1 marker for radio module on board
