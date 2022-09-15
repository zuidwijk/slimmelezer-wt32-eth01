# SlimmeLezer + esp32 + ethernet
![WT32-ETH01](/images/WT32-ETH01.jpg)
## WT32-ETH01 based
For this version I was looking for an esp32 and ethernet. The best version I could find is the [WT32-ETH01 from wireless-tag](http://www.wireless-tag.com/portfolio/wt32-eth01/). These boards can be found on [AliExpress](https://nl.aliexpress.com/wholesale?SearchText=WT32-ETH01) for [around €15](https://nl.aliexpress.com/item/1005004432624600.html) (including shipping)
### Pin-out
![WT32-ETH01 pin-out](/images/WT32-ETH01_pinout.png)
### Serial pins for flashing the esp32
Looking at the top-part of the pin-out image, those pins are used for flashing the esp32. The TXD0, RXD0, IO0, EN, GND and 3v3. Those pins are directly connected to the ch340 serial chip.
### Serial pin for data
In the middle left you see the RXD and TXD for serial communication. Only the RXD is used as DSMR is an oneway communication protocol.
### Ethernet pins for ESPHome
The following settings are used in case of using ESPHome:
```yaml
ethernet:
  type: LAN8720
  mdc_pin: GPIO23
  mdio_pin: GPIO18
  clk_mode: GPIO0_IN
  phy_addr: 1
  power_pin: GPIO16
```
See more information about ethernet and esphome on [their website](https://esphome.io/components/ethernet.html#configuration-for-wireless-tag-wt32-eth01).
## The device
![](/images/IMG_8460.jpeg)
![](/images/IMG_8462.jpeg)
![](/images/IMG_8463.jpeg)
