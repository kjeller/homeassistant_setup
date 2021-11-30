# Homeassistant setup

Hardware and OS configurations for my headless Raspberry Pi (RPI) homeassistant setup. 

## Hardware setup

- [Raspberry Pi 3B+](https://www.raspberrypi.com/products/raspberry-pi-3-model-b-plus/)
  - Host running Home Assistant
- [M.2 DTE/DCE expansion hat for RPI](https://www.conrad.se/p/m2-dtedce-expansionskort-foer-raspberry-pi-1487097)
  - Expansion hat for RPI M.2 SSD integration
- [Phoscon Conbee II](https://phoscon.de/en/conbee2)
  - Makes RPI a [Zigbee](https://csa-iot.org/) gateway

[Read this doc for configuration required to boot from an SSD.](docs/RPI_USB_boot.md)

## Image and software setup

Both the image Makefile and ansible script assumes that the RPI hostname is statically configured in the router DHCP/DNS settings. A gateway can be configured to resolves dns requests between hosts on a local network.
Example configuration in OpenWRT:
```
config host
       option mac '<MAC ADDRESS HERE>'
       option name 'homeassistant'
       option dns '1'
       option ip '<IP ADDRESS HERE>'
```

Index of repository:
- [image](image/)
  - Creates an image file with preconfigured hostname and ssh settings 
- [configure_host](configure_host/)
  - Run script on homeassistant node to create user and install applications required for homeassistant to run. 

### Homeassistant configuration
My homeassistant configuration can be found [here](https://github.com/kjeller/homeassistant_config).
