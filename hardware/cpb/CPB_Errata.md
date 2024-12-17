- Revision 1  
    - RN components need proper fanout, some currently have vias in pads which causes marginal clearance.
    - Power Glitch needs to be fixed  
        - C5(22nF) needs to be moved between the gate and +VBATT rather than the
gate and ground. (Electrically, everything should remain the same except
for the specified change above).  
    - Q3 doesn't have a base resistor and otherwise needs correction.  After lifting Q3, it is possible to program the microcontroller.
