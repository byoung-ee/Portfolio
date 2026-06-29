Power Sheme:
<img width="1246" height="581" alt="image" src="https://github.com/user-attachments/assets/3a77e5b4-6cdc-4695-9e7b-f66552d21da0" />

Most reference designs use an integrated PMIC to generate core voltages, with several external LDOs or other regulators to regulate the remaining rails. This scheme is fairly novel because it would use a supervising MCU to manage startup sequencing, pulling each regulator EN high as described in DS181 AC/DC characteristics. This gives the power scheme a lot of flexibility; for example it uses commodity level regulators (that still meet noise/regulation requirements), and the number of regulated rails can be easily increased/decreased depending on changing requirements without making huge design changes. Additionally how the system responds to faults (like brownouts) can be easily adjusted without figuring out some obscure Renesas PMIC programming protocol. 

Tentative bottom placement (unrouted):
<img width="817" height="620" alt="image" src="https://github.com/user-attachments/assets/ee69844f-c695-4d07-8407-0d0cd21aed69" />

Most of the power rails can actually be sufficiently routed on the bottom layer alone, on a 4 layer board this would leave the top and in2 layers for fanout, which makes it mathematically possible to fanout all FPGA pins on a 4 layer board. However this isn't really ideal for signal integrity as only the top layer would be referenced to ground, and it would be basically impossible to route DDR or MGT traces/differentials. 6 layer stackups are much better for this application. 

<img width="500" height="250" alt="image" src="https://github.com/user-attachments/assets/1378b34a-a2db-429d-91b5-979a7e3e2b9b" />

Common stackups exist where there is a thick core followed by two internal layers seperated by thin prepreg, then the top/bottom layer again seperated by thin prepreg. This effectively means that if the in1 and 4 layers are continuous ground planes then the remaining 4 layers all have a solid ground reference, which makes the stackup much more suitable for DDR. It also makes fanning and breaking out the bulk I/O to the mezzanine connectors much easier. 

Hirose DF40C mezzanine connectors are available in various board heights, depending on capacitor selection the lowest height of 1.5mm could be used (this would prohibit carrier board placement under the SOM which might be detrimental). I would probably fit with the 3mm height. Shielded variant or effective pin selection on unshielded variant should support MGT transceivers.


<img width="1426" height="612" alt="image" src="https://github.com/user-attachments/assets/8bc1186e-3d3b-4fcc-81f0-f8c1dd8c45be" />

Meandered fanout from DDR3 lower bytelane to random pin assignments on FPGA bytegroup T1. Lots of ratsnest crossovers. Meanders are needed to reduce length difference before length matching squiggles.
<img width="1214" height="566" alt="image" src="https://github.com/user-attachments/assets/44e44fa5-2483-4683-97dd-d466bddbcb3b" />

Ordered pin assignments after running simple geometric solver script. No ratsnest crossovers. Being able to quickly assign bytegroup pins to ordered, fanned out, DDR side traces means you can quickly trial different bytegroup assignments to find a somewhat optimal set of pin assignments for routing.

<img width="1339" height="613" alt="image" src="https://github.com/user-attachments/assets/a858e608-0e4f-4ac9-8675-f8f6ba516854" />

Tentative routing and length matching to maximum bytelane trace length of 26.9 mm. Probably needs more clearance on the meanders to avoid crosstalk.

<img width="1356" height="659" alt="image" src="https://github.com/user-attachments/assets/c883f627-3d69-460a-b320-275ff8b6cd9e" />
Same for upper bytelane, but length matched to 26.9-2*effective via length top layer/in3. Because this won't be fabricated with backdrilled vias it's important to route internal traces on the lower (ground referenced to layer 4) layers, to minimize via stubs. Unfortunately the back layer is mostly components so in3 is a compromise. There's still space to route the critical differential clocks, however.
