# SlimmeLezer WT32-ETH01 + water meter
![WT32-ETH01](/images/WT32-ETH01.jpg)
## SlimmeMeter WT32-ETH01
For this modification an additional 5-pin cornered header was soldered to the side of the WT32-ETH01 to allow for the connection of an inductive proximity sensor ([LJ12A3-4-Z/BX](https://www.kiwi-electronics.com/en/inductive-proximity-sensor-switch-panel-mount-3177)) using a [logic level shifter](https://www.amazon.nl/s?k=3.3V+naar+5V+shifter) to bring the 5V sensor signal down to 3.3V.

### Modifying the WT32-ETH01
The level shifter connects to 5V+GND, 3V+GND and a GPIO on the WT32-ETH01; looking at the [pin-out](/images/wt32_pinout_large.png) those are neatly next to each other when using GPIO17, allowing for an extra set of headers to be soldered to the side of the pcb.
![WT32-ETH01 additional 5-pin header](/images/wt32-water-001.jpeg)
![WT32-ETH01 additional 5-pin header](/images/wt32-water-002.jpeg)

### Adding headers to the logic level shifter
Using a set of female 6 pin headers, with extended pins allows for the proximity sensor to be connected to the protruding pins, whilst connecting the WT32-ETH01 to the other side. The headers were bend 90 degrees to allow for a smaller footprint and the extended pins on the 3V side were clipped, since you need to bring up or down the voltage on the sensor side.
![WT32-ETH01 additional 5-pin header](/images/wt32-water-003.jpeg)

### Connecting WT32-ETH01, shifter and proximity sensor
The WT32-ETH01 with GPIO17 connected to one of the four low-level signal channels and the 3.3V and 5V lines connected to the respective voltage inputs of the shifter. The sensor is connected to the extended pins on the 5V side, making sure the signal wire is connected to the cooresponding high-level signal channel of the GPIO17 connection.
![WT32-ETH01 additional 5-pin header](/images/wt32-water-004.jpeg)

## YAML modifications
Some general changes were done, like setting the webserver version 3, renaming services to actions, setting the friendly name and taking care of the strapping warning on GPIO5. Below are the actual changes for the water meter to function.

### substitutions
Set the water_meter_pin
```yaml
substitutions:
...
  water_meter_pin: GPIO17
```
### on_boot
Restore the saved value from the global water_meter_total on the pulse_meter.
```yaml
  on_boot:
    then:
...
      - pulse_meter.set_total_pulses:
          id: water_meter
          value: !lambda "return id(water_meter_total);"
```

### actions
Create an action to set the water_meter_total from Home Assistant, via code or the actions tab in developer tools.
```yaml
api:
  actions:
...
    - action: set_water_meter_total
      variables:
        new_water_meter_total: int
      then:
        - logger.log:
            format: Setting water meter total from %d to %d.
            args: [id(water_meter_total), new_water_meter_total]
        - pulse_meter.set_total_pulses:
            id: water_meter
            value: !lambda 'return new_water_meter_total;'
```

### globals
Create a global variable to hold the water_meter_total during reboots and power losses.
```yaml
globals:
...
  - id: water_meter_total
    type: int
    restore_value: yes
```

### sensor
The water meter is implemented with the pulse_meter component, allowing to register flow rate and using its total for water consumed. The lambda in the filters ia used to write the value to the global water_meter_total on change, so it will survice reboots and power outages. To cope with the dirty signal coming from the level shifter or the sensor itself the internal filter mode was set to pulse, otherwise it seemed to trigger on the raising and falling edge.
```yaml
sensor:
...
  - platform: pulse_meter
    pin:
      number: ${water_meter_pin}
      inverted: true
      mode:
        input: true
    internal_filter_mode: "PULSE"
    internal_filter: 100ms
    id: water_meter
    name: "Water Flow Rate"
    unit_of_measurement: "l/min"
    icon: "mdi:pulse"
    timeout: 60s
    total:
      id: water_consumed
      name: "Water Consumed"
      accuracy_decimals: 3
      unit_of_measurement: "m³"
      icon: mdi:water
      device_class: water
      state_class: total_increasing
      filters:
        - multiply: 0.001
        - lambda: |-
            id(water_meter_total) = id(water_consumed).raw_state;
            return x;
```
