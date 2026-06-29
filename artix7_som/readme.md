<img width="1426" height="612" alt="image" src="https://github.com/user-attachments/assets/8bc1186e-3d3b-4fcc-81f0-f8c1dd8c45be" />

Meandered fanout from DDR3 lower bytelane to random pin assignments on FPGA bytegroup T1. Lots of ratsnest crossovers. Meanders are needed to reduce length difference before length matching squiggles.
<img width="1214" height="566" alt="image" src="https://github.com/user-attachments/assets/44e44fa5-2483-4683-97dd-d466bddbcb3b" />

Ordered pin assignments after running simple geometric solver script. No ratsnest crossovers. Being able to quickly assign bytegroup pins to ordered, fanned out, DDR side traces means you can quickly trial different bytegroup assignments to find a somewhat optimal set of pin assignments for routing.

<img width="1339" height="613" alt="image" src="https://github.com/user-attachments/assets/a858e608-0e4f-4ac9-8675-f8f6ba516854" />

Tentative routing and length matching to maximum bytelane trace length of 26.9 mm. Probably needs more clearance on the meanders to avoid crosstalk.

<img width="1356" height="659" alt="image" src="https://github.com/user-attachments/assets/c883f627-3d69-460a-b320-275ff8b6cd9e" />
Same for upper bytelane, but length matched to 26.9-2*effective via length top layer/in3. Because this won't be fabricated with backdrilled vias it's important to route internal traces on the lower (ground referenced to layer 4) layers, to minimize via stubs. Unfortunately the back layer is mostly components so in3 is a compromise. There's still space to route the critical differential clocks, however.
