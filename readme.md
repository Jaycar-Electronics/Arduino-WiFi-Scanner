# Arduino WiFi Scanner

This is a simple tool using just four Duinotech parts that can sit on your workbench and quickly tell you what WiFi networks are nearby and their relative strengths. If you're always working with different WiFi networks like we are, or even have a few XC4614's running in hotspot mode, it's handy to know which networks are up and which are not.

## Features

- Displays 5 most powerful WiFi networks nearby.
- Shows relative strength.
- Automatic update every 5 seconds.

![Main Image](images/hcf-august-wifi-scanner.png)

## Table of contents

- [Arduino WiFi Scanner](#Arduino-WiFi-Scanner)
  - [Features](#Features)
  - [Table of contents](#Table-of-contents)
  - [Bill of Materials](#Bill-of-Materials)
    - [Optional Extras](#Optional-Extras)
  - [Table of Connections](#Table-of-Connections)
  - [Programming](#Programming)
  - [How it Works](#How-it-Works)
  - [Improvements](#Improvements)
  - [Contributing](#Contributing)
  - [Futher reading](#Futher-reading)

## Bill of Materials

| Qty | Code                                     | Description    |
| --- | ---------------------------------------- | -------------- |
| 1   | [XC4410](https://jaycar.com.au/p/XC4410) | Uno Main Board |
| 1   | [XC4614](https://jaycar.com.au/p/XC4614) | WiFi Shield    |
| 1   | [XC4614](https://jaycar.com.au/p/XC4614) | Nokia Screen   |
| 1   | [WC6026](https://jaycar.com.au/p/WC6026) | Jumper leads   |

### Optional Extras

- If you don't mind soldering, you can replace the jumper leads with [HM3207](https://jaycar.com.au/p/HM3207) headers placed back to back, as shown below
- Using a Leonardo might be a simplier idea, as you will then be able to leave the switches in the ON position;
  - Simply change the code to send AT commands to the `Serial0` connection instead of `Serial`

## Table of Connections

The shield simply sits on top of of the uno, then the Nokia Screen is connected to the data pins from the shield. Use the below table as reference.

| Uno with shield | LCD      | Function           | Colour wire |
| --------------- | -------- | ------------------ | ----------- |
| D2              | VCC      | Power to LCD       | Black       |
| D3              | GND      | Ground for LCD     | Brown       |
| D4              | SCE      | Serial Chip Enable | Red         |
| D5              | RST      | Reset              | Orange      |
| D6              | D/C      | Data/Command       | Yellow      |
| D7              | DN(MOSI) | Serial Data        | Green       |
| D8              | SCLK     | Serial Clock       | Blue        |

If you want the backlight to be connected, simply ground the LED pin.

## Programming

The current code does not use exteneral libraries but we are happy to bring in a pull request for something that does. This code even has inbuilt fontmap data.

The ESP shield has 2 small connections on the shield for the serial communication between the uno and the ESP; You will want to turn them off when you are programming the uno, then turn them back on when you are running the sketch. This is similar to our latest [XC4411](https://jaycar.com.au/p/XC4411) Uno with WiFi board.

You can also use the Leonardo [XC4430](https://jaycar.com.au/p/XC4430) which has a seperate USB serial to connect to the computer and can make this process a lot simplier.

## How it Works

The WiFi shield provides most of the smarts in detecting the WiFi networks, the Uno simply tells the shield what to do and displays the results.

The shield firmware is configured to accept `AT` Commands from the uno which is how most modems and IoT devices work in the firmware level:

- `ATE0`
  - This tells the shield not to echo back the commands that it receives; We won't need it for this project but it makes it easier to debug if you enable this line when trying to communicate to the ESP shield.
- `AT+CWMODE=1`
  - This command to tell the shield to only behave as a station, and not an access point. Again, because the shield isn't busy trying to provide an access point, it has more time to do what we want it to do.
- `AT+CWLAP`
  - This command asks the shield to return data about networks that are nearby. Each line of data looks like below:

```plaintext
+CWLAP:(3,"Arduino",-41,"74:da:38:3c:71:99",1,-47)
```

Here you can see that the SSID is the second field in the returned data ( `"Arduino"`) and the signal strength is followed (`-41`). The signal strength is negative because of how decibels are representend. you can read more about decibels and signal strength in the [Futher reading](#Futher-reading) section.

The first parameter is the security type of the SSID, and you'll find that ours ( and yours too, hopefully) will be a `3`. Other values are below:

| Code | Meaning                                                                                                               |
| ---- | --------------------------------------------------------------------------------------------------------------------- |
| 0    | Open, or no security.                                                                                                 |
| 1    | WEP security; has a password but not the best security                                                                |
| 2    | WPA security; Better, but still not great                                                                             |
| 3    | WPA2 security; Most routers and networks that have a password should use this method, it is most common and very safe |
| 4    | WPA_WPA2 securiry; Uses both WPA and WPA2, which is safe for new devices but can also work with older devices,        |

Note that the last one includes both WPA and WPA2, which means despite having WPA2, the original WPA can still be cracked, and is not considered safer than using just WPA2 by itself. What it lacks in security it makes up for in compatability.

## Improvements

To create a more rigid assembly, solder together two of the eight-way headers from the [HM3207](https://jaycar.com.au/p/HM3207) pack:

![Header](images/duinotech-wifi-scanner-improvements-1.jpg)

![Header 2](images/duinotech-wifi-scanner-improvements-2.jpg)

The below photo shows how the WiFi Scanner looks when it is assembled using the [HM3207](https://jaycar.com.au/p/HM3207) to give a more compact arrangement. Note how we only use the last seven pins of the header adapter so that the LED pin is not connected.

![ Solder](images/duinotech-wifi-scanner-improvements-3.jpg)

## Contributing

We definitely would love contributions from you guys in regards to this project; cleaning up the code; commenting the code; product suggestions and improvements. Even change it all together to use something like the new [XC4411](https://jaycar.com.au/p/XC4411) Uno with WiFi and a [XC4630](https://jaycar.com.au/p/XC4630) Display shield. This is a great way to learn about programming, get help when you need it; and attach your name to something great.

## Futher reading

- WiFi Security modes
  - <https://www.howtogeek.com/204697/wi-fi-security-should-you-use-wpa2-aes-wpa2-tkip-or-both/>
- AT Command list interpreted by the esp, as well as `CWLAP` command.
  - <https://github.com/espressif/esp8266_at/wiki/CWLAP>
- WiFi Signal Strength
  - <https://eyesaas.com/wi-fi-signal-strength/>
