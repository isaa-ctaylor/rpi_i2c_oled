IC2 OLED controller for Raspberry PI
=======================

Python library to enable 128x32 pixel OLED for Raspberry Pi (both 32 and 64bit).

This repository has been broken out to work as a standalone service and will work on a standard Raspberry Pi running Raspian.

<a href="https://www.buymeacoffee.com/jedimeat" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/white_img.png" alt="Buy Me A Coffee" style="height: auto !important;width: auto !important;" ></a>

[![release][release-badge]][release-url]
![downloads][downloads-badge]
<br>
<br>

## Some Teaser Screenshots.
| Welcome | HA Splash | CPU Stats | RAM Stats | Storage Stats | Network Stats | Exit Screen | 
|-----------|-----------|-----------|-----------|---------------|---------------|---------------|
| ![Welcome][welcome-url] | ![Splash][splash-url] | ![CPU Stats][cpu-stats-url] | ![RAM Stats][ram-stats-url] | ![Storage Stats][storage-stats-url] | ![Network Stats][network-stats-url] ![Exit][exit-url]

<br>
<br>

Adafruit Python SSD1306
=======================
**This addon leverages the original [Adafruit Python SSD1306](https://github.com/adafruit/Adafruit_Python_SSD1306) and [GPIO](https://github.com/adafruit/Adafruit_Python_GPIO) libraries, which have been deprecated. However, I have taken the nessassary parts out of this and bundled them into this I2C module avoiding the need for GPIO and relying on the Raspberry Pi's I2C setup.**

Original Repo: https://github.com/adafruit/Adafruit_Python_SSD1306

Originally designed specifically to work with the Adafruit SSD1306-based OLED displays ----> https://www.adafruit.com/categories/98

Adafruit invests time and resources providing this open source code, please support Adafruit and open-source hardware by purchasing products from Adafruit!
<br>
<br>

[Gareth Cheyne](https://github.com/garethcheyne/HomeAssistant) & Ultronics
======================
Special thanks to [Gareth Cheyne](https://github.com/garethcheyne/HomeAssistant) for his intial version of this project.
After the removal of GPIO support from Home Assistant, the referenced addon no longer worked for me, so I took the initial project apart, and smashed it together with the Adafruit I2C libraries removing the GPIO requirements.

Additionally, the original build didn't work on 64bit versions of the Raspberry Pi, nor would it work as a stand alone service.
<br>
<br>

Home Assistant Support
======================
This repository has been broken out to work as a standalone service and will work on a standard Raspberry Pi running Raspian.

Any screens which are dependent on Home Assistant (e.g. ```Splash```) will be automatically disabled

Home Assistant variant of this build can be accessed from [HomeAssistant_Addons](https://github.com/crismc/homeassistant_addons)
<br>
<br>

Hardware Setup
===============
You can use 0.91 Inch 128X32 I2C module, as long as it is registered on /dev/i2c-1 which is the Rasperry Pi default.

I purchased this [MakerHawk I2C OLED Display Module I2C Screen Module 0.91" 128X32 I2C](https://www.amazon.co.uk/gp/product/B07BDFXFRK/ref=ppx_yo_dt_b_asin_title_o07_s00?ie=UTF8&psc=1)

Pin setup:
--------------
```
- PIN1 : Power (3.3V / VCC)
- PIN3: SDA (I2C Data)
- PIN5: SCL (I2C Clock)
- PIN14: Ground (0V)
```

Enable I2C on the Raspberry Pi
```
sudo raspi-config
# Interface Options > I2C
```

One-Step Automated Install
----------------
Those who want to get started quickly and conveniently may install the RPI_I2C_OLED using the following command:
```
curl -sSL https://raw.githubusercontent.com/crismc/rpi_i2c_oled/v1.0.0/basic-install.sh | sudo bash
```

Installing From Source
------------------------
Initial apt-get installs:
```
sudo apt-get install i2c-tools git vim
```

Test I2C device is working:
```
$ i2cdetect -y 1
```

Install Python3 dependencies
```
sudo apt-get install python3-dev python3-smbus python3-pil
```

Checkout this code
```
git clone git@github.com:crismc/rpi_i2c_oled.git
cd rpi_i2c_oled
```

Test OLED
```
cd rpi_i2c_oled
python3 display.py
```

Create a service 
-----------------

Copy the repo file to /etc:
```
sudo cp -ri ../rpi_i2c_oled /etc
```

Create a sym link of the service file in /etc/systemd/system, and reload it
```
sudo ln -s /etc/rpi_i2c_oled/oled.service /etc/systemd/system/oled.service
sudo systemctl daemon-reload
```

Test it out
```
sudo service oled start
sudo service oled stop
sudo service oled restart
```

Start on boot
```
sudo systemctl enable oled.service
```

## Configuration Options

You can modify the display of the content by editing the ```options.json``` file.

By default the ```options.json``` is loaded from the same directory as the ```display.py```. 

Note: The keys are case **in**sensitive

If you want to change this location, simply pass in the path to the desired config using the flags -c or --config:

```
python3 display.py -c /path/to/options.json
```
or
```
python3 display.py --config /path/to/options.json
```

| Name                 | Type    | Requirement  | Description                                            | Default             |
| ---------------------| ------- | ------------ | -------------------------------------------------------| ------------------- |
| i2c_bus     | int  | **Required** | I2C bus number. /dev/i2c-[bus number]                  | `1`                 |
| Temperature_Unit     | string  | **Required** | Display the CPU temperature in C or F                  | `C`                 |
| Default_Duration     | int     | **Required** | How long in seconds to display each screen by default. Ignored if specified on specific screen  | `10`                |
| Show_Welcome_Screen  | boolean | **Required** | Show the animated Welcome to `hostname` screen         | `true`              |
| Show_Splash_Screen  | boolean | **Required** | Show the Home Assistant core and version screen. I you're not using Home Assistant, leave this false         | `false`              |
| Show_Network_Screen  | boolean | **Required** | Show the Network Information screen         | `true`              |
| Show_CPU_Screen  | boolean | **Required** | Show the CPU Information screen         | `true`              |
| Show_Memory_Screen  | boolean | **Required** | Show the Memory Information screen         | `true`              |
| Show_Storage_Screen  | boolean | **Required** | Show the Storage Information screen         | `true`              |
| Screenshot  | boolean or string | **Optional** | Saves a screenshot of the screen to the specified path, or to './img/examples/' if set to True         | `false`              |
| *_Screen_Limit    | int | **Optional** | Number of times to show the screen in the cycle. Once limit is reached, display will no longer appear                            | null              |
| *_Screen_Duration | int | **Optional** | How long in seconds to display the screen              | `10`              |

<br>
<br>

## Logs & Debugging
As logging is initiated before the party begins, log levels are defined outside of the ```options.json```.

Therefore, to enable debugging, when running ```display.py``` add ```-d``` or ```--debug``` to the command.

e.g.:

```
$ python3 display.py -d
INFO:Config:Loading config: /etc/rpi_i2c_oled/options.json
INFO:Config:Home Assistant is not supported on this instance
INFO:Config:'welcome' limited to 5 iterations
INFO:__main__:'welcome' is being processed
INFO:Screen:'WelcomeScreen' created
INFO:Screen:'WelcomeScreen' rendering
```

<br>
<br>
<br>

# Legal Stuff Required By Adafruit
As part of the library leveraged to produce this service, the below text needs to be copied... *(yawn)*

However, you can ignore the install instructions below as they've been replaced by the above.

[Original Adafruit Repository](https://github.com/adafruit/Adafruit_Python_SSD1306)

*DEPRECATED LIBRARY* Adafruit Python SSD1306
=======================

his library has been deprecated! We are leaving this up for historical and research purposes but archiving the repository.

We are now only supporting the use of our CircuitPython libraries for use with Python.

Check out this guide for info on using OLEDs with the CircuitPython library: https://learn.adafruit.com/monochrome-oled-breakouts/python-wiring

---------------------------------------

Python library to use SSD1306-based 128x64 or 128x32 pixel OLED displays with a Raspberry Pi or Beaglebone Black.

Designed specifically to work with the Adafruit SSD1306-based OLED displays ----> https://www.adafruit.com/categories/98

Adafruit invests time and resources providing this open source code, please support Adafruit and open-source hardware by purchasing products from Adafruit!

Installing
----------

```
sudo python -m pip install --upgrade pip setuptools wheel
sudo pip install Adafruit-SSD1306
```

Or alternatively:

```
sudo python -m pip install --upgrade pip setuptools wheel
git clone https://github.com/adafruit/Adafruit_Python_SSD1306.git
cd Adafruit_Python_SSD1306
sudo python setup.py install
```

Copying
-------

Written by Tony DiCola for Adafruit Industries.
MIT license, all text above must be included in any redistribution

<!-- References -->
[release-badge]: https://img.shields.io/github/v/release/crismc/rpi_i2c_oled?style=flat-square
[downloads-badge]: https://img.shields.io/github/downloads/crismc/rpi_i2c_oled/total?style=flat-square
[release-url]: https://github.com/crismc/rpi_i2c_oled/releases
[welcome-url]: /img/examples/welcome.png?raw=true
[cpu-stats-url]: /img/examples/cpu.png?raw=true
[ram-stats-url]: /img/examples/memory.png?raw=true
[storage-stats-url]: /img/examples/storage.png?raw=true
[network-stats-url]: /img/examples/network.png?raw=true
[splash-url]: /img/examples/splash.png?raw=true
[exit-url]: /img/examples/static_goodbye.png?raw=true