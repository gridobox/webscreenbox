WebScreen
=========

This is how to provision single board kiosk on Armbian with Chromium/Firefox/Midori.

Use a board of your choice from list of supported platforms on https://www.armbian.com/download/

Get to all available version at https://dl.armbian.com/ and of course go through documentation on https://docs.armbian.com/

PAY ATTENTION TO correct base image for your board, i.e. Lime vs. Lime2 !!!


Features
--------
The only function is to display URL in web browser in fullscreen.

This setup is to be used receptions, offices, public places, generally anywhere where a screen with a web page comes useful.

URL can be edited in `/opt/config/simple_url.txt`

List of available browsers:
 - Chromium
 - Firefox 45
 - Midori

tested:
- Armbian_5.35_Lime_Ubuntu_xenial_next_4.13.16.img
- Armbian_5.59_Lime2_Ubuntu_bionic_next_4.14.65.img


bootstrap image to sd sdcard

boot for the first time

root passwd is 1234 by default

create user admin

reboot

# if network is accessible, unattended updates may strike in any time, check with `htop` command and be patient.

# to set access to the box you will need it's <ip> address

# copy ssh key into /home/admin/.ssh/authorized/keys
ssh-copy-id admin@<ip>
ssh admin@<ip>
su - root
mkdir ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
vim ~/.ssh/authorized_keys
# Ubuntu Bionic - Ansible errors: fix missing python - create symlink to python3
cd /usr/bin/
ln -s python3 python
# set sudo access without password
echo "admin ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# loggout
exit

# set local ansible ./host file

# run ansible commands
ansible-playbook -i hosts 01_update_armbian.yml
ansible-playbook -i hosts 02_install_tools.yml
ansible-playbook -i hosts 03_admin_setup.yml
ansible-playbook -i hosts 05_system_setup.yml
ansible-playbook -i hosts 06_install_software.yml
...
ansible-playbook -i hosts 99_reboot.yml


po nainstalovani je treba

do chromu doinstalovat rucne extension:
  - tab rotate
    - menu > More tools > Extensions
    - search on web
    - "tab rotate" - the one with velociped
    - install, confirm, right click on icon
      set config to remote URL http://localhost/tab_rotate.json

    https://chrome.google.com/webstore/detail/tab-rotate/pjgjpabbgnnoohijnillgbckikfkbjed
    https://github.com/KevinSheedy/chrome-tab-rotate.git


    ansible-playbook -i hosts 99_reboot.yaml



Create Matrix image
--------------------

5. nakopcit do .img na pocitaci
dd if=/dev/mmcblk0 of=/root/lime-dashboard-6xx.img bs=8M

6. zmensit partition na 2GB http://raspberrypi.stackexchange.com/questions/5440/copy-existing-raspbian-installation-to-smaller-sd-card
 # nejdriv je treba pripojit blokove zarizeni ze souboru pomoci loop
 modprobe loop
losetup /dev/loop0 lime2-dashboard-6xx.img
partprobe /dev/loop0

 # stav pratition
fsck.ext4 -fy /dev/loop0p1

 # A] zmenseni partition v terminalu http://geekofpassage.blogspot.co.uk/2014/01/im-running-out-of-space-gee-thats-never.html
 resize2fs /dev/loop0p1 <pocet_bloku>
 # kdyz hodi hlasku: resize2fs: Nová velikost je menší než minimum (665451)
 # je treba podle toho upravit velikost, ale nejlepsi pridat aspon par set MB
 resize2fs /dev/loop0p1 665451

 # B] zmenseni partition v GUI
gparted /dev/loop0
 # cca 2048MiB

 # overit vylsedek v fdisku
fdisk /dev/loop0
 Zařízení     Zaveditelný Start   Konec Sektory  Size Id Druh
 /dev/loop0p1              8192 5332991 5324800  2,6G 83 Linux

 # odpojeni blokoveho zarizeni
losetup -d /dev/loop0

 # oriznuti souboru .img
 # vysledna velikost v bytech se zjisti z fdisk (vyse) jako koncovy sektor (+1?) * 512 (viz. units)
 v tomto prikladu je to
echo "(5332991+1) * 512" | bc

 truncate --size=2730491904 lime-dashboard-6xx.img

# k otestovani image lokalne
losetup /dev/loop0 lime2-dashboard-6xx.img
mount /dev/loop0p1 /mnt/iso1/
... nautilus
umount /dev/loop0p1 /mnt/iso1/
losetup -d /dev/loop0
