# Yes, we can scan IEs!

IEEE 802.11 (WLAN) devices send broadcast control packets called *beacons* at regular intervals.
Beacons contain information such as the name of the network (SSID), the channel/frequency 
used by the device, etc.

In addition to this information, some IEEE 802.11 (outdoor device) vendors use proprietary 
extensions, in the form of IEEE 802.11 Information Elements (**IEs**),
in the beacons of their devices.
These IEs usually contain the name of the device to help in the scanning process: 
when users perform "site surveys" from the web interface of their devices (with original firmwares),
they will find, next to the SSID, channel, and other information, the device name of the device generating
the beacons.

In Linux based OSes (including OpenWRT/LEDE) this feature is not ready out of the box,
but the scripts here included can help to scan, decode and generate these proprietary IEs.

These scripts are designed to work also with the busybox shell on OpenWRT/LEDE.

## sitesurvey

This script outputs a table like the one below:

                  BSS                          SSID        SIGNAL    FREQ            HOSTNAME
    a2:63:91:aa:aa:aa                        D-Link    -82.00 dBm    2412                    
    c0:4a:00:bb:bb:bb                     ninux.org    -79.00 dBm    2437          experiment
    44:d9:e7:cc:cc:cc                          ubnt    -51.00 dBm    5220                 fox

To install, just download it and make it executable:

    wget --no-check-certificate https://raw.githubusercontent.com/cl4u2/ieswescan/master/sitesurvey
    chmod +x sitesurvey

To use it try:

    ./sitesurvey wlan0


## generatevendorelement

hostapd already includes a configuration parameter called `vendor_elements` which can be configured to send custom IEs.
This script takes as input a name (e.g. the hostname) and generates a corresponding `vendor_elements` configuration value for `hostapd.conf`.

Example:

    wget --no-check-certificate https://raw.githubusercontent.com/cl4u2/ieswescan/master/generatevendorelement
    chmod +x generatevendorelement
    ./generatevendorelement "$(uci get system.@system[0].hostname)"
    ...
    ./generatevendorelement "experiment"
    dd22000c42000000011e000000001f660902ff0f6578706572696d656e74000000000000

We can then take the output of this script and add it to our `hostapd.conf`. For example:

    # Additional vendor specific elements for Beacon and Probe Response frames
    # This parameter can be used to add additional vendor specific element(s) into
    # the end of the Beacon and Probe Response frames. The format for these
    # element(s) is a hexdump of the raw information elements (id+len+payload for
    # one or more elements)
    vendor_elements=dd22000c42000000011e000000001f660902ff0f6578706572696d656e74000000000000
    
 
