The main idea behind this project was that the internet is becoming increasingly "vertical"; many apps/websites are designed primarily for mobile usage, with desktops being a faint afterthought. 
However, many monitors can be physically rotated, vertically<->horizontally, easily and quickly. The issue lies with manually setting the orientation of the monitor in the operating system; ie. in windows this can only be changed in the display settings. 
Monitors do not "know" what orientation they are in. They simply display images, and do not have the hardware to detect orientation.
A solution to this is to couple an accelerometer to the orientation of the monitor. The easy, and boring, way to do this is to simply attach an externally powered accelerometer board to the monitor (tape).
However this gets messy fast, the power and data cables get tangled with the existing monitor connections, making it a temporary solution at best. 
Powering the accelerometer board directly from the monitor and transmitting the accelerometer data back to the host PC wirelessly solves this, with exactly 0 additional cables.

The complex problem is receiving power from the monitor. While some monitors have USB passthrough and therefore a nice 5v DC supply, most do not. In fact, almost none of the connectors on a standard monitor have any power supply whatsoever, the exception being 100mA aux 3.3v on DisplayPort inputs.
Unfortunately, most monitors have a single DisplayPort, which is often the main display input. This leaves exactly 0 conventional, viable power supplies. 

HDMI uses a lot of power to receive signals from the host PC (up to ~50 mW). This is largely due to transceiver TMDS terminations being present on the input side, which are directly tied to the connector data nets. These terminations are implemented as 50 ohm parallel terminations to aVCC, a 3.3v source. A logical zero from the host PC sinks around 10mA of current per termination resistor, which for a typical data pattern (~50% low) is substantial across the 8 terminations available. This effectively means we can sink 5mA*8=~40mA of 3.05V across the 50 ohm terminations, while technically remaining within HDMI design specification. 

