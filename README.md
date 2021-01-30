# asset
System administration process to report server status to the **do it yourself home automation system** (DIYHAS). 
## Description: 
This is my latest **Raspberry Pi** project that implements an administration server to collect and report status to my "do it yourself home automation" system.  The application requires **Raspbian OS** and is written in **python3**. I usually create a **systemd service** so the application runs at boot.

## Installation: 
Installation is a two step process. First clone the repository, and then install dependent software with the **requiements.txt** file. 

- Step 1 - clone this repository
```
git clone https://github.com/parttimehacker/asset.git
```
- Step 2 - Install required software - MQTT and RPI.GPIO libraries
```
cd admin
pip install -r requirements.txt
```
## Usage: 
You need to decide whether you want to manually run the application or have it started as part of the boot process. I recommend making a **Raspbian OS systemd service**, so the application starts when rebooted or controled by **systemctl** commands. The **systemd_script.sh** creates a admin directory in **/usr/local directory**. The application files are then copied to this new directory. The application will also require a log file in **/var/log directory** called admin.log
### Manual or Command Prompt
To manually run the application enter the following command (sudo may be required on your system)
```
sudo python3 admin.py --mq <MQTT_BROKER> --location <LOCATION>
```
- <MQTT_BROKER> I use the Open Source Mosquitto broker and bridge
- <LOCATIONR> is the room where the server is located 
- Host names or IP address can be used.
### Raspbian systemd Service
First edit the **admin systemd service** and replace the MQTT broker and web server values with their host names or IP addresse. A systemd install script will move files and enable the applicaiton via **systemctl** commands.
- Run the script and provide the application name **admin** to setup systemd (the script uses a file name argument to create the service). 
```
vi admin.service
./systemd_script.sh asset
```
This script also adds four aliases to the **.bash_aliases** in your home directory for convenience.
```
sudo systemctl start asset
sudo systemctl stop asset
sudo systemctl restart asset
sudo systemctl -l status asset
```
- You will need to login or reload the **.bashrc** script to enable the alias entries. For example:
```
cd
source .bashrc
```
### MQTT Topics and Messages
The application subscribes to two MQTT topics and publishes six status messages. Three are are sent at initialization and then handled by a **diy/system/who** message. Three other messages are sent every 15 minutes after calculating an average. The first three are:
```
self.host = socket.gethostname()
self.os_version_topic = "diy/" + self.host + "/os"
self.pi_version_topic = "diy/" + self.host + "/pi"
self.ip_address_topic = "diy/" + self.host + "/ip"
```
The timed messages are:
```
self.host = socket.gethostname()
self.cpu_topic = "diy/" + self.host + "/cpu"
self.celsius_topic = "diy/" + self.host + "/cpucelsius"
self.disk_topic = "diy/" + self.host + "/disk"
```
- The **diy/system/who** sends local server information to the MQTT Broker. 
## Contributing: 
- Adafruit supplies most of my hardware. http://www.adafruit.com
- My "do it yourself home automation" system leverages the work from the Eclipse IOT Paho project. https://www.eclipse.org/paho/
- I use the PyCharm development environment https://www.jetbrains.com/pycharm/
## Credits: 
Developed by parttimehacker.
## License: 
MIT

