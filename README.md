# Garamatic 9 Automation 

This is a simple enough project that requires a bit of soldering. There are lots of videos and ideas on this kind of project. This one is specifically for my preference which is Tasmotized Sonoff hardware talking to Home Assistant controlling a Garamatic 9 door opener. But it will probably work with a whole range of Hormann and other similar openers.

What I used:
1. Sonoff SV (e.g. https://www.ebay.co.uk/itm/163818100797)
2. Magnetic contact switch (e.g. https://www.ebay.co.uk/itm/293305357069)
3. Approx 4m of R/B/Bl/Y four core alarm cable
4. Small ABS box (e.g. https://www.aliexpress.com/item/1005001526260365.html)
5. A handful of male and female Dupont connectors
6. Some sticky pads and tie-wraps.
7. An old USB lead modified to provide a 5V power supply

This is a schematic of the Sonoff SV board:

![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/09a391b0-f4af-4da9-adb9-28a977036b34)

The first task is to solder male pins in the switch out (To motor) pads and to solder a jumper across the switch in.
(I also solder male pins into the four pads gnd, tx, rx, 3v3 for flashing). Finally break off the two resistors by inserting a small screwdriver between the resistor and the jumpers and gently twisting it where shown.
Now you can flash Tasmota onto the SV.  I used <a href="https://github.com/tasmota/tasmotizer">Tasmotizer</a> and an FTDI232 set at 3.3v. After the flash is successful, unplug the FTID which will power off.

Next step is to power up using the 5V USB power lead and connect the SV to your wifi network. Once on-line open up a web browser to configure the SV. Follow through all the normal steps including the MQTT set up. Configure Module as type Sonoff SV (0).  I set all GPIOs as none except GPIO14 which has to be set as Switch 1.

![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/e1128ca1-a8f9-46cb-81ad-13bfbbd4374c)

I also entered the following configuration by the Console interface

```
Backlog TimeZone 99; TimeDST 0,0,3,1,1,60; TimeSTD 0,0,10,1,2,0; Time
SetOption56 1
SetOption57 1
Teleperiod 30
Switchmode1 1
Switchmode1 15
PowerOnState1 0
PulseTime1 10
```

Go over to Home Assistant.  I like to to keep my configuration.yaml file compact, so I put my extra text in "included" files with 
```
cover: !include cover.yaml
mqtt: !include mqtt.yaml
```
So create and save the following files (or append to existing files) in HA /config folder paying careful attention to indents:

**cover.yaml**
```
    garage_door_left:
      unique_id: garagedoorleft
      friendly_name: "Garage Door Left"
      device_class: shade
      value_template: >-
        {% if states('binary_sensor.garage_door_left_state') == 'on' %}
          open
        {% else %}
          closed
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
```
**mqtt.yaml**
```
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
  - name: Garage Door Left Switch
    unique_id: garagedoorleftswitch
    state_topic: "stat/SV2/POWER"
    command_topic: "cmnd/SV2/POWER"
    payload_on: "ON"
    payload_off: "OFF"
```
**script.yaml**
```
garage_door_left_operate:
  alias: 'Garage Door Left Operate'
  sequence:
  - service: switch.turn_on
    data: {}
    target:
      entity_id: switch.garage_door_left_switch
  mode: single
  icon: mdi:garage-alert-variant
```

Normal process about checking yaml and restarting HA apply. When HA is restarted add the new cover to a lovelace tab ready to test.

Back to the soldering iron and solder/crimp female dupont connectors onto the red/yellow of the magnetic contact switch and onto the red/black/blue/yellow of one end of the four core cable.

Connect the magnetic contact switch to GPIO14 and power up SV using the 5V USB lead.  You can now the test the operation by pretending to be the motor and make/break the magnetic contact in response to red lights flashing on the SV.

When you are OK, power down, connect the four core cable and fit everything in the box. You should have something that looks like this.

![2023-07-16 17 43 49x](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/bd16d2fe-29dd-4a0b-bd67-7669949a43f3)

Next you can install the box and magnetic contact switch. You only need to worry about getting the contact sensor to close when the door is fully closed as the default should be open/"unsafe". I found it easiest to position both sides of the sensor on sticky tape before drilling the metal to mount the cabled part on the frame, then partially opening the door to drill and mounting the other part. One screw will suffice!

![2023-07-24 17 00 19x](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/098fa4fd-3d8b-4336-b9f3-d3958ec3d1ca)

THe four core cable has to be run along the top of the motor track. This will be very dirty so clean it before fastening the cable with sticky pads and cable ties.

Unplug the motor unit and remove the cover. There is a connection shown as 24V but this cannot be used as it is only live when motor is powered on, so instead get power for the SV from the adjacent terminals and use the SV 1 second pulse out put to switch the motor on, thus:
![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/80e6e218-3657-4db0-88ea-fe7885d8749b)

The numbers refer to diagrams in the <a href="https://www.garador.co.uk/media/files/fitting-instructions/garamatic-9.pdf"> Garamatic 9 Manual</a>.

You should end up with something like this.

![2023-07-25 12 35 46x](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/fed90791-c61b-4542-bea2-669d2d4e83a3)

That's it really. My biggest challenge was getting reliable wifi in my garage but it's all fine now.

![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/df1b7456-3a14-4253-b02d-e36cab69d846)

Good luck!
