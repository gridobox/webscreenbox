
# copy ready to use image
dd if=<your_image>.img of=/dev/mmcblk0 bs=1M

# eject card - unmount
# re-insert card - mount

# change hostname
mcedit /media/vasek/dd92a82e-e478-4525-ae54-faa1cc961072/etc/hostname

# set timezone
unlink /media/vasek/dd92a82e-e478-4525-ae54-faa1cc961072/etc/localtime
ln -s /usr/share/zoneinfo/Europe/London /media/vasek/dd92a82e-e478-4525-ae54-faa1cc961072/etc/localtime

# eject card - unmount


# insert card into the SBC device
# plut to HDMI, network and power up

# for wireless network, have working HW (check in `dmesg`)
nmtui
# or for more complicated WPA2/Enterprise (user TAB with nmcli)
nmcli connection add \
 type wifi con-name "MySSID" ifname <ifname> ssid "MySSID" -- \
 wifi-sec.key-mgmt wpa-eap 802-1x.eap peap \
 802-1x.phase2-auth mschapv2 802-1x.identity "USERNAME"
nmcli connection up "MySSID" --ask
# visually check all is ok

# record MAC into HW user_help_text (read from DHCP server or login and `ifconfig` or so)
