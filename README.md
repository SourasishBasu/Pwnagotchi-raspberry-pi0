<p align="left"> 
   <br>
   <img src="https://github.com/SourasishBasu/Pwnagotchi-raspberry-pi0/blob/84acfe20ae628b100076527942129d52e1135720/assets/pwnagotchi.png"/>
</p>

[Official Documentation](https://pwnagotchi.ai/intro/)

# Pwnagotchi-Raspberry-Pi0

 A recreation of the original Pwnagotchi but with mods to support the officially unsupported Raspberry Pi 3.5 inch GPIO based screen. 
 
Pwnagotchi is an A2C-based “AI” powered by bettercap that learns from its surrounding WiFi environment in order to maximize the crackable WPA key material it captures (either through passive sniffing or by performing deauthentication and association attacks). This material is collected on disk as PCAP files containing any form of crackable handshake supported by hashcat, including full and half WPA handshakes as well as PMKIDs.

 
## Hardware

- A Raspberry Pi Zero WH
- A microSD card (8GB minimum recommended, preferably of good quality and speed)
- A decent quality micro-USB cord that allows data transfer
- A 3.5″ Touch Screen LCD Raspberry Pi Display


## Configuration:

- Download and Install [balenaEtcher](https://etcher.balena.io/#download-etcher) and [PuTTy](https://www.putty.org/)
- Using balenaEtcher flash this [image](https://drive.google.com/file/d/1CO0-47CPS6muQ8sjIbm6xOnuHNLkvWMV/view?usp=sharing) onto the microSD card
- Once boot folder is created add the config.toml file.
  - To whitelist a particular network from getting pwned (e.g. your home network) add the network SSID within main.whitelist in the config.toml file.
- Insert the microSD card into the RPi0 and connect it to a PC using a data cable from the data port.

  <p align="center"> 
      <br>
      <img src="https://github.com/SourasishBasu/Pwnagotchi-raspberry-pi0/blob/8375bb5ee962219fba5de3d2255b88d4f2d7235d/assets/raspi0.png"/>
      <br><b>Raspberry Pi Zero Components</b>
  </p>

- Open view network connections and the device should show up as a RNDIS device.
  - In case it does not appear, open Device Manager.
  - Go to Network Adapters > Action > Add Drivers
  - Browse and add the folder containing the RNDIS.inf driver file.
  - The RPi0W device should be visible as a RNDIS device now.
- Go to the RPi0W device Properties GUI > IPv4 > Properties
  - Add static IP: 10.0.0.1
  - Add subnet mask: 255.255.255.0
  - Add default gateway: 10.0.0.1
  - Add DNS (if required): 8.8.8.8
- Test your connection to the RPi0 in cmd with:

  ```bash
  ping 10.0.0.2
  ```

 ## Connection

 Change the following settings in PuTTy and press Load to access the RPi0 using SSH.
 
 <p align="center"> 
  <img src="https://github.com/SourasishBasu/Pwnagotchi-raspberry-pi0/blob/06e5ea16d7ca78be0a6148529df75c438af54eb0/assets/putty.png" />
   <br><b>PuTTy Configuration for SSH Access</b>
</p>


- Default password: 'raspberry'


 ## Custom screen mod

- To enable Internet access to the device:
  
  ```bash
  nano /etc/resolv.conf
  ```

  - Edit the file to add
  
    ```bash
    dns 8.8.8.8
    ```
- ```bash
  sudo apt update && upgrade
  ```
- To configure the screen
  - Fit the screen onto the GPIO pins on the RPi0 board
  
    <p align="center"> 
      <img src="https://github.com/SourasishBasu/Pwnagotchi-raspberry-pi0/blob/4b7f3bf4dc8611646c52964b131ef126a1eda59f/assets/IMG-20230802-WA0026.jpg" />
    </p>

  - ```bash
    sudo rm -rf LCD-show
    git clone https://github.com/waveshare/LCD-show.git
    cd LCD-show/
    chmod +x LCD35-show
    sudo nano /usr/local/lib/python3.7/dist-packages/pwnagotchi/ui/hw/spotpear24inch.py
    ```
  - Replace the file contents with screenmod.txt contents

  - ```bash
    ./LCD35-show lite
    ```

- The screen will boot. Don't disconect or the whole process may have to be repeated again.

<p align="center"> 
  <img src="https://github.com/SourasishBasu/Pwnagotchi-raspberry-pi0/blob/4b7f3bf4dc8611646c52964b131ef126a1eda59f/assets/IMG-20230802-WA0021.jpg" />
</p>


## Usage

Pwnagotchi will start in AUTO mode by default.

It has a webUI hosted at http://10.0.0.2:8080/ and the username & password are both 'changeme' by default.

<p align="center"> 
  <img src="https://github.com/SourasishBasu/Pwnagotchi-raspberry-pi0/blob/8702a6467f7a288840aff517e5c250e1223706fb/assets/face.png" />
</p>

- Custom configurations are stored in
  
  ```bash
   /etc/pwnagotchi/config.toml
  ```

## Decryption

- Handshakes captured by Pwnagotchi are saved to
  
  ```bash
  /root/handshakes/
  ```
- SSH into the device via PuTTy
- Copy the .pcap file of the captured handshake to be decrypted from the root directory into /home/pi

   ```bash
   sudo su
   cd ..
   cd boot
   cp -r /handshakes /home/pi
   exit
   ```
- Remove the microSD card from the RPi0 and access the copied .pcap file from a PC
- Run aircrack-ng in the handshakes folder to use the RockYou wordlist to decrypt

   ```bash
   aircrack-ng example-handshake.pcap -w /usr/share/wordlists/rockyou.txt
   ``` 
