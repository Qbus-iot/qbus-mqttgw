# qbus-mqttgw

This is the repository for the Qbus Mqtt Gateway application. With this application you can communicate with all Qbus CTD controller over an MQTT server.

# How to use
The software requires a few dependecies. 
sudo apt-get install -y tftp unzip

If you don't have a MQTT server, we recommand using Mosquitto: sudo apt-get install -y mosquitto

### Setting up Qbus MQTT client
First we need to unzip the tar files (this example uses the arm version, if you are installing on a Linux machine, use the correct version - arm / x86 / x64):

`tar -xf qbusMqtt/qbusMqtt/qbusMqttGw/qbusMqttGw-arm.tar`

Then we create the locations needed for the software:
```
sudo mkdir /usr/bin/qbus
sudo mkdir /opt/qbus
```

And copy the files to the correct locations:

```
sudo cp -R qbusMqtt/qbusMqtt/qbusMqttGw/qbusMqttGw-arm/fw/ /opt/qbus/
sudo cp qbusMqtt/qbusMqtt/qbusMqttGw/qbusMqttGw-arm/puttftp /opt/qbus/
sudo cp qbusMqtt/qbusMqtt/qbusMqttGw/qbusMqttGw-arm/qbusMqttGw /usr/bin/qbus/
```
Make files executable:
```
sudo chmod +x /usr/bin/qbus/qbusMqttGw
sudo chmod +x /opt/qbus/puttftp
```

To use the client, we recomment to use a service.
Create a new file:

`/lib/systemd/system/qbusmqtt.service`

And enter the following:
```
[Unit]
Description=MQTT client for Qbus communication
After=multi-user.target networking.service

[Service]
ExecStart= /usr/bin/qbus/./qbusMqttGw -serial="QBUSMQTTGW" -daemon true -logbuflevel -1 -logtostderr true -storagedir /opt/qbus -mqttbroker "tcp://localhost:1883" -mqttuser <user> -mqttpassword <password>
PIDFile=/var/run/qbusmqttgw.pid
Restart=on-failure
RemainAfterExit=no
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

If your Mqtt broker is on another device, change "tcp://localhost:1883".  
Replace \<user\> and \<password\> by you mosquitto credentials.


Now it's time to reload the servies:

`sudo systemctl daemon-reload`

Enable the qbusmqtt service:

`sudo systemctl enable qbusmqtt.service`
  
Finally start the service:

`sudo systemctl start qbusmqtt.service`
