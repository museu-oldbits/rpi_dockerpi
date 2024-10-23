# How to install 
## Readme
The shell script will detect the platform of your Raspberry Pi from Raspberry Pi 4B and Raspberry Pi 5, and also detect the architechture of your CPU from 32bit to 64bit. and will install the right binary file to /usr/sbin location. 
## Pre-install 
```bash
sudo apt update
sudo apt upgrade -y
sudo apt -y install build-essential gcc g++ cmake
```
## Download repository  
```bash
git clone https://github.com/geeekpi/dockerpi.git
cd dockerpi/powerboard/
sudo ./install.sh
```
## Checking Method
Please make sure the blue color LED has been light up for a while, it means that the MCU on powerboard has detected and communicated with Raspberry Pi properly. 
* Click Power button to initializing shutdown processure. 
If the operating system has been shutdown properly after clicking the power button once. it means that the driver has been installed properly. 
otherwise, you need to reinstall the driver again. 
## How to compile the binary file by yourself.
* Just navigate to location `src` in repository and just execute `make` will compile the latest binary file according to your current system compiler.
* For example:
* For 64bit 
```bash
cd dockerpi/powerboard/src/64bit
make 
```

* For 32bit 
```bash
cd dockerpi/powerboard/src/32bit
make 
```
## How to clean up the compile files.
```bash
make clean
```
## I2C enable for RaspBerry Pi 5 with Raspi-OS (debian)
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
