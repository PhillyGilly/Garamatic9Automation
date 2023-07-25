# Garamatic 9 Automation 

This is a simple enough project that requires a bit of soldering. There are lots of videos and ideas on this kind of project. This one is specifically for my preference which is Tasmotized Sonoff hardware talking to Home Assistant contolling a Garamatic 9 door opener. But it will probably work with a whole range of Hormann and other similar openers.

What I used:
1. Sonoff SV (e.g. https://www.ebay.co.uk/itm/163818100797)
2. Magnetic contact switch (e.g. https://www.ebay.co.uk/itm/293305357069)
3. Approx 4m of R/B/Bl/Y four core alarm cable
4. Small ABS box (e.g. https://www.aliexpress.com/item/1005001526260365.html)
5. A handful of male and female Dupont connectors





![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/60cfa5d0-a5a1-4cfb-afa1-2bd650da8bbd)

My config:
Module type Sonoff SV (0)
GPIO all none
GPIO14 switch 1 ??

Backlog TimeZone 99; TimeDST 0,0,3,1,1,60; TimeSTD 0,0,10,1,2,0; Time
SetOption56 1
SetOption57 1

Teleperiod 30
Switchmode1 1
Switchmode1 15
PowerOnState1 0
PulseTime1 10

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




![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/1fae7832-9901-4714-ae9d-fc9e29d65ba7)

![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/1def7664-b5b5-4ef1-a0ab-75fc3b889812)

![2023-07-24 17 00 19](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/f7a34c53-6cc3-4958-99a1-c24e9b0c9967)
![2023-07-16 17 43 49](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/afae2f65-65d6-4334-9891-679b19f057cc)
![2023-07-16 18 40 21](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/2aa252b0-a159-4b33-8343-06fa8d5a45b2)
![image](https://github.com/PhillyGilly/Garamatic9Automation/assets/56273663/2bdc5b0c-001d-4316-ae36-7f4ae3927d42)
