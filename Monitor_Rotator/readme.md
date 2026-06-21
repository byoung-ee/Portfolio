The main idea behind this project was that the internet is becoming increasingly "vertical"; many apps/websites are designed primarily for mobile usage, with desktops being a faint afterthought. 
However, many monitors can be physically rotated, vertically<->horizontally, easily and quickly. The issue lies with manually setting the orientation of the monitor in the operating system; ie. in windows this can only be changed in the display settings. 
Monitors do not "know" what orientation they are in. They simply display images, and do not have the hardware to detect orientation.
A solution to this is to couple an accelerometer to the orientation of the monitor. The easy, and boring, way to do this is to simply attach an externally powered accelerometer board to the monitor (tape).
However this gets messy fast, the power and data cables get tangled with the existing monitor connections, making it a temporary solution at best. 
Powering the accelerometer board directly from the monitor and transmitting the accelerometer data back to the host PC wirelessly solves this, with exactly 0 additional cables.

The complex problem is receiving power from the monitor. While some monitors have USB passthrough and therefore a nice 5V DC supply, most do not. In fact, almost none of the connectors on a standard monitor have any power supply whatsoever, the exception being 500mA DP_PWR 3.3V on DisplayPort inputs.
Unfortunately, most monitors have a single DisplayPort, which is often the main display input. This leaves exactly 0 conventional, viable power supplies. 

HDMI uses a lot of power to receive signals from the host PC (up to ~130 mW). This is largely due to transceiver TMDS terminations being present on the input side, which are directly tied to the exposed connector transceiver pairs. These terminations are implemented as 50 ohm parallel terminations to aVCC, a 3.3v source. A logical zero from the host PC sinks around 10mA of current per termination resistor, which for a typical data pattern (~50% low) is substantial across the 8 terminations available. This effectively means we can sink 5mA*8=~40mA of 3.05V across the 50 ohm terminations, while technically remaining within HDMI design specification. 

This leads to another challenge- how do you consistently sink no greater than 5mA per termination while maximising the available power budget. Connecting the terminations directly to a device side 3.3V net is clearly a bad idea; RF MCUs can draw up to 200mA while transmitting, and even if that were possible within the voltage drop of the terminations it would fry the HDMI aVCC supply instantly. And it isn't possible because it would result in a voltage drop brownout+the catastrophic overcurrent of the HDMI circuit. Initially, my idea was to use a discrete linear current regulator- tie all terminations together through a shunt resistor, V=IR, hold I at 40mA etc. This would use a low VGS PFET in conjunction with a current sense OP-amp to limit the input current to 40mA through to a large capacitor or supercapacitor. Given a (very) low average current draw of the MCU, the voltage of the capacitor net would eventually charge to ~3.3V, which could then be regulated down to an MCU operating voltage of 1.8V or 3V.

The version 2 hardware instead uses a buck converter with op-amp regulated voltage foldback. 6 of the available 8 terminations are used as power terminations, with variable voltage across the termination according to current draw. The remaining 2 are used as reference voltages with minimal current draw; and remain at ~3.3v. The differential voltage between the "reference" and "power" nets is used by the differential op amp to sense average current through the power terminations; and raise the feedback voltage of the buck converter high when input current is exceeded. This effectively uses the 50 ohm terminations themselves as current sense resistors. This current foldback protects the HDMI transceivers from any possible damage.

The differential voltage can approach or even exceed the reference/VDD "3.3v", so a relatively specialized over the rails op amp (MCP6021) was required. 

This current regulation feeds into a large (5.7 mF) polymer capacitor, which over 100s of mS charges to slightly less than 3.3v. This capacitor net is then regulated to 3.3v exactly using a low quiescent, ultra low input voltage boost converter (TPS61299). This provides stable and low noise power for the MCU, accelerometer, and associated RF circuitry. 

The MCU chosen was the ESP32C3. In hindsight, this was a poor decision as the BLE power consumption is diminished by sharing circuitry with WIFI. During transmission this can spike to >300 mA at 3.3v, and even in the lowest power, connection maintaining, state still uses ~20 mA on average. This stresses the power budget at best and causes intermittent brownouts when transmissions are not optimized. Eg. to even establish a BLE connection the buffering capacitance needed to be doubled to 11.4 mF. The in-progress version 3 uses a NRF54 series MCU, with less than a tenth of the power consumption, at 1v8.







