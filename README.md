# HomeAssistant ADS Interface - polling

HomeAssistant includes a component called "ads" to interface with Beckhoff PLCs. This is a fork of said component.

## Official documentation and code

Official documentation:
https://www.home-assistant.io/integrations/ads/

Original code:
https://github.com/home-assistant/core/tree/dev/homeassistant/components/ads

## Why this fork?

The "official" ads integration does not work in my setup. Reading and writing variables through active polling works fine, but somehow setting up "Device Notifications" is failing. In turn, all ADS controls show up as "failed to initialize" and can not be operated.

This fork rewrites the integration to use polling (that is, HA actively asks the PLC for the variable values every 30 seconds) to avoid these problems. This is not an optimal solution, but it works for me.

## How to install

Put this integration into the `<homeassistant_config_dir>/custom_components/` folder. `<homeassistant_config_dir>` is the directory where `configuration.yaml` is.

If using git:
```
cd <homeassistant_config_dir>/custom_components
git clone https://github.com/orwell96/ads_polling.git
```

Note, if you used the HassOS image to install HA, it is not possible to install this integration. Please install a full-featured OS on the RPi and install HomeAssistant by one of the other ways, such as "HomeAssistant Core" as described [here](https://www.home-assistant.io/installation/).

## How to use

Nothing has changed feature-wise. Just follow the official documentation (see above), but replace "ads" with "ads_polling". This module can be used as a drop-in replacement if the official one does not work.

Note that everything except switches is not tested by me yet. If you experience problems, please add an issue.

If you want to change the polling interval, change [this line](https://github.com/orwell96/ads_polling/blob/aeb93ea15afad41c95c172b9111334a562eb00e9/__init__.py#L49).

## Other things to note when using ADS in HomeAssistant

This is a list of possible troubleshooting approaches if you can't get ADS to work. I'm assuming that you don't use the HassOS image, if you do, forget it.

### Parameter Size not correct

From what I experienced, this error message stems from a bug in the ADS client library used by HA, and is not the "actual" error message that you are supposed to see. In my case, it was actually the case that the AMS NetID of the PLC itself was not correctly configured, and the actual error message was "Target Machine Not Found".

To find out the real error message, you need to do the following:
1. Install "wireshark" on the device running homeassistant
2. Set it to capture on the network interface that the PLC is connected to, click Start Capture
3. in the filter field, enter `ip.addr == <ip of PLC>`
4. start homeassistant

You should now see the ADS packets exchanged between homeassistant and the PLC. If you find the answer packet to your connection request, wireshark will tell you the correct error number and error message.

### More?

If you have something to add to this list, feel free to open a PR.
