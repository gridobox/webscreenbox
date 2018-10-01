
This project based on Armbian is aiming to provide solid web browser on a single board computer.

0. HW requirements
- Single Board Computer compatible with Armbian
- power adapter
- HDMI screen and cables
- network cable and/or wireless adapter

1. First goal is to build yourself an matrix image to generate clones from. The same apply if you like to build one off box.

Installation on SD card is automated using Ansible, details are in `provisioning` folder.

Once you are happy with your card follow instructions on the end to create your matrix image (mother for all the clones).

2. Copy ready to use image to a SD Card (presumably new one) and follow instructions in PRODUCTION.md




Management commands
-------------------

# managing sd card freeze
ansible-playbook -i hosts 96_overlayroot_on.yml
ansible-playbook -i hosts 97_overlayroot_off.yml



# administracni prikazy
ansible-playbook -i hosts 90_install_url_configs.yml
ansible-playbook -i hosts 98_reset_browser.yml
ansible-playbook -i hosts 99_reboot.yml
