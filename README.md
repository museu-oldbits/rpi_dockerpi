# Docker Pi Modules Demo Code
* Nightlight Hat Board For Raspberry Pi 
* 4 Channel Relay Hat Board For Raspberry Pi
* Powerboard 
* SensorHub
# Requirement
* i2c-tools *(Raspberry PI 5 with debian RPi OS, see below)
* smbus2  
# How To Setup
## Install i2c-tools 
* sudo apt-get update 
* sudo apt-get -y install i2c-tools
* sudo apt-get -y install git
* pip3 install smbus2
## Detect Device Address
* i2cdetect -y 1 
## Clone Repository
* cd ~
* git clone https://github.com/geeekpi/dockerpi.git
* cd dockerpi/Nightlight/
* sudo ./Nightligh.sh  
or 
* sudo python ./Nightlight.py
or 
* gcc Nightlight.c -lwiringPi -o nightlight
* sudo ./nightlight
----
And the same way to 4 Channel Relay Hat Board.
Have Fun.

## *I2C enable for RaspBerry Pi 5 with Raspi-OS (debian)
Create enable_i2c.sh script:
```bash
cd ~
nano enable_i2c.sh
```

Copy script inside enable_i2c.sh:
```bash
#!/bin/bash

# Check root execution script
if [ "$EUID" -ne 0 ]; then
  echo "Please use root shell (sudo)"
  exit 1
fi

# Enable i2C on /boot/config.txt
CONFIG_FILE="/boot/config.txt"

if grep -q "^dtparam=i2c_arm=on" "$CONFIG_FILE"; then
  echo "I2C já está habilitado no $CONFIG_FILE."
else
  echo "Enabling I2C on $CONFIG_FILE..."
  echo "dtparam=i2c_arm=on" >> "$CONFIG_FILE"
fi

# Load i2c-dev module
echo "Loading i2c-dev module..."
modprobe i2c-dev

# Verify i2c module load
if lsmod | grep -q i2c_dev; then
  echo "i2c-dev module successfully charged."
else
  echo "i2c-dev module charge error."
  exit 1
fi

# Verify /dev/i2c-1 device available
if [ -e /dev/i2c-1 ]; then
  echo "/dev/i2c-1 available."
else
  echo "/dev/i2c-1 unnavailable. Need to reboot system."
  echo "Do you want reboot now? (s/n)"
  read -r resposta
  if [ "$resposta" == "s" ]; then
    echo "System rebooting..."
    reboot
  else
    echo "System needs to be rebooted to finish I2C enabling."
  fi
fi
```
Press Ctrl + O to save para salvar enable_i2c.sh then Ctrl + X to get out from nano editor.

Make enable_i2c.sh runnable
```bash
chmod +x enable_i2c.sh
```
Now run enable_i2c.sh
```bash
sudo ./enable_i2c.sh
```
Continue to install modules.
