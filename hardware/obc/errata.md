- Revision 1
    - From PyCubed, CRITICAL: high energy protons cause single-event latchup.  see: https://github.com/pycubed/forums/discussions/87
       > We have found the ATSAMD51 microcontroller used on PyCubed Mainboards (v00 - v05) exhibiting a surprisingly high sensitivity to 50MeV protons. Our KickSat-2 and VR3X missions had zero issues with the ATSAMD51 and the part does have some radiation literature. But after troubleshooting unexplained reboots on our recent PY4 mission, we decided to test it ourselves.
    - U103 pin marking isn't visible with installed part / is under body
    - Too many components require manual rotation during JLCPCB ordering
    - From JLCPCB DFM review:
        - L101 doesn't fit; substituting 0806 inductor, DFE201610E-100M=P2
          C426327 for order
        - D501 is a pseudo SOD-123 in a power-optimized package but we have
          ordinary SOD-123 pads; substituting 1N5819HW-7-F p/n C82544 for
          order
