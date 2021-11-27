# Setting up a headless rpi

Information derived from: [Link](https://www.raspberrypi.com/documentation/computers/configuration.html#setting-up-a-headless-raspberry-pi)

## Setup

Connect the rpi SD card or USB storage to a Linux or Windows computer. 
- Windows should be able to detect the boot partition. 
- Linux should be able to detect both the boot and root partition.

## Connect to WIFI

Create file /boot/wpa_supplicant.conf to automatically login to a WIFI network:
```
country=SE
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
network={
    ssid="********"
    psk="********"
    key_mgmt=WPA-PSK
}
```

## Enable SSH

Enable SSH by creating an empty file /boot/ssh or /boot/ssh.txt
Now SSH should be enabled. By default the user and password are pi and raspberry, respectively.

# Ubuntu/Linux basics

## Commands for add/delete/modifying a user
```bash
# Add user
sudo adduser <username>

# Delete user
sudo deluser <username>

# Add user to sudoers group
sudo adduser <username> sudo
```

## SSH basics

```bash
# Copy public SSH key for host A to server B
# On host A run the following:
ssh-copy-id <username>@<hostname server B>

# This should allow a login for host A to server B
ssh <username>@<hostname server B>
```

The next step is to disable password logins and only use public key authentication.
Make sure the following lines are added/uncommented in /etc/ssh/sshd_config:
```
PasswordAuthentication no
PubkeyAuthentication yes
```

Then restart ssh `systemctl restart sshd.service`.
`ssh <username>@<hostname server B>` should no longer grant a password authentication prompt for server B.
