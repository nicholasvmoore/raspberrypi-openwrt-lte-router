# Raspberry Pi 3 OpenWRT LTE Router

## Packages to install
```bash
opkg update && opkg install minicom tmux usb-modeswitch kmod-usb-net-qmi-wwan uqmi kmod-usb-serial-wwan kmod-usb-serial-option qmi-utils iptables-mod-ipopt
```

## Connect to Ceullular Network (Auto)


## Connect to Cellular Network (Manual)
These commands will connect you to your cellular network
1. Verify Link Protocol `qmicli -p -d /dev/cdc-wdm0 --wda-get-data-format`
  - You should see **raw_ip** under **Link Layer Protocol**
2. Verify the **raw_ip** in the kernel
```bash
root@OpenWrt:~# cat /sys/class/net/wwan0/qmi/raw_ip
N
root@OpenWrt:~# echo 'Y' > /sys/class/net/wwan0/qmi/raw_ip
root@OpenWrt:~# cat /sys/class/net/wwan0/qmi/raw_ip
Y
```
3. Create qmi-network.conf
```bash
APN=fast.t-mobile.com
PROXY=yes
```
4. Start Network `qmi-network /dev/cdc-wdm0 start`
5. Manual Start Network
```bash
qmicli --device=/dev/cdc-wdm0 --device-open-proxy --wds-start-network="ip-type=4|6,apn=fast.t-mobile.com" --client-no-release-cid
```
6. Manual Stop Network
```bash
qmicli --device=/dev/cdc-wdm0 --wds-stop-network=2267681600
```
7. Adjust ttl
```bash
iptables -t mangle -I POSTROUTING -o wwan0 -j TTL --ttl-set 67
ip6tables -t mangle -I POSTROUTING -o wwan0 -j HL --hl-inc 1
```

## Common Command Reference
```bash
# Network Details
qmicli -d /dev/cdc-wdm0 --nas-get-system-selection-preference

# Operation Names
qmicli -d /dev/cdc-wdm0 --nas-get-operator-name

# Get System Info
qmicli -d /dev/cdc-wdm0 --nas-get-system-info

# Get Data Format
qmicli -p -d /dev/cdc-wdm0 --wda-get-data-format
```

## References
- [http://trac.gateworks.com/wiki/wireless/modem](http://trac.gateworks.com/wiki/wireless/modem)
- [https://github.com/openwrt/openwrt/issues/6196](https://github.com/openwrt/openwrt/issues/6196)
