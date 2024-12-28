- Revision 1  
    - RN components need proper fanout, some currently have vias in pads which causes marginal clearance.
    - Power Glitch needs to be fixed  
        - C5(22nF) needs to be moved between the gate and +VBATT rather than the gate and ground. (Electrically, everything should remain the same except
for the specified change above).  
    - Q3 doesn't have a base resistor and otherwise needs correction.  After lifting Q3, it is possible to program the microcontroller.
    - DACs glitch at power up.  May be possible to address with power-up sequence (getting 3V3 going before 5V).
    - DAC outputs seem noisy.  May be an artifact of probe technique / test environment ground loops.  Need to float setup and better characterize.
    - Power switches turn off pretty slow, inhibiting PWM.  R96 could get smaller, but that'll get wasteful in power pretty quickly.
