# SlimmeLezer WT32-ETH01 + water meter
![WT32-ETH01](/images/WT32-ETH01.jpg)
## SlimmeMeter WT32-ETH01 ba
For this modification an additional 5-pin cornered header was soldered to the side of the WT32-ETH01 to allow for the connection of an inductive proximity sensor (LJ12A3-4-Z/BX) via a logic level shifter.

### Pin-out
![WT32-ETH01 pin-out](/images/wt32_pinout_large.png)

### Pins for level shifter and pulse_meter
The level shifter connects to 5V+GND, 3V+GND and a GPIO, looking at the pin-out those are neatly next to each other when using GPIO17.
![WT32-ETH01 additional 5-pin header](/images/wt32-water-001.jpeg)
![WT32-ETH01 additional 5-pin header](/images/wt32-water-002.jpeg)

