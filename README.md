# Garamatic 9 Automation 

This is a simple enough project that requires a bit of soldering. There are lots of videos and ideas on this kind of project. This one is specifically for my preference which is Tasmotized Sonoff hardware talking to Home Assistant contolling a Garamatic 9 door opener. But it will probably work with a whole range of Hormann and other similar openers.

What I used:
1. Sonoff SV (e.g. https://www.ebay.co.uk/itm/163818100797)
2. Magnetic contact switch (e.g. https://www.ebay.co.uk/itm/293305357069)
3. Approx 4m of R/B/Bl/Y four core alarm cable
4. Small ABS box (e.g. https://www.aliexpress.com/item/1005001526260365.html)
5. A handful of male and female Dupont connectors
6. An old USB lead mofified to provide a 5V power supply

This is a schematic of the Sonoff SV board:
![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/60cfa5d0-a5a1-4cfb-afa1-2bd650da8bbd)

The first task is to solder male pins in the switch out connectors and to solder a jumper across the switch in.
(I also solder male pins into the four connectors for flashing). Finally break off the two reistors either side of the xxxxx.
Now you can flash Tasmota onto the SV.  I used a FTDI232 set at 3.3v. After the flash is successful unplug the FTID which will power off.

Next step is to power up using the 5V USB power lead and connect the SV to your wifi network. Once on-line open up a web browser to configure the SV. Follow through all the normal steps including the MQTT set up. Configure Module as type Sonoff SV (0).  I set all GPIOs as none except GPIO14 which has to be set as switch 1.
![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/e1128ca1-a8f9-46cb-81ad-13bfbbd4374c)

I also entered the following configuration by the Console interface

Backlog TimeZone 99; TimeDST 0,0,3,1,1,60; TimeSTD 0,0,10,1,2,0; Time
SetOption56 1
SetOption57 1

Teleperiod 30
Switchmode1 1
Switchmode1 15
PowerOnState1 0
PulseTime1 10

Go over to Home Assistant.  I like to to keep my configuration.yaml file compact, so I put my extra text in "included" files with 
>cover: !include cover.yaml
>mqtt: !include mqtt.yaml
So create and save the following files in HA /config folder

cover.yaml
    garage_door_left:
      unique_id: garagedoorleft
      friendly_name: "Garage Door Left"
      device_class: shade
      value_template: >-
        {% if states('binary_sensor.garage_door_left_state') == 'on' %}
          closed
        {% else %}
          open
        {% endif %}
      open_cover:
        service: script.open_garage_door_left
      close_cover:
        service: script.close_garage_door_left
      icon_template: >-
        {% if states('binary_sensor.garage_door_left_state') == 'on' %}
          mdi:garage-variant
        {% else %}
          mdi:garage-open-variant
        {% endif %}

mqtt.yaml
binary_sensor:
  - name: "Garage Door left state"
    unique_id: garagedoorleftstate
    state_topic: "tele/SV2/SENSOR"
    payload_on: "ON"
    payload_off: "OFF"
    qos: 0
    device_class: opening  
    value_template: "{{value_json.Switch1}}"
    icon: "mdi:garage-variant"
switch:





Back to the soldering iron and solder/crimp female dupont connectors onto the red/yellow of the magnetic contact switch.  Connect it to GPIO14 and test .




![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/1fae7832-9901-4714-ae9d-fc9e29d65ba7)

![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/1def7664-b5b5-4ef1-a0ab-75fc3b889812)

![2023-07-24 17 00 19](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/f7a34c53-6cc3-4958-99a1-c24e9b0c9967)
![2023-07-16 17 43 49](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/afae2f65-65d6-4334-9891-679b19f057cc)
![2023-07-16 18 40 21](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/2aa252b0-a159-4b33-8343-06fa8d5a45b2)
![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/2bdc5b0c-001d-4316-ae36-7f4ae3927d42)
