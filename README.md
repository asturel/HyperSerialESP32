# HyperSerialESP32
  
Exposes USB high speed serial port at 2Mb baud for driving led strip using NeoPixelBus library. It's intended to replace slow Arduino solutions (level shifter 3.3V to 5v may be required). **Data integrity check (Fletcher's checksum) included in new 'Awa' protocol for HyperHDR. So no more random flashing caused by serial transmission errors.** That option must be checked in HyperHDR to make system works as on the screen below.  
  
**Make sure that your serial chip on the ESP32 can handle 2Mb speed: for example CP2102 can't as its max speed is 1Mb and you can compile a version for that speed but I think that's not the point, cheap CH340G can do it without any problems.**  
  
There is also my fork named HyperSerialWLED available with the support for the AWA serial protocol at @2Mb speed for both ESP8266 and ESP32: https://github.com/awawa-dev/HyperSerialWLED Can't guarantee it will work as stable as HyperSerialESP32 because WLED has a lot of other things to do in the backgrounds (ex. handling Wifi) and timing control for the serial port could be at danger for larger number of LEDs. But you don't need to abandon all the benefits offered by the WLED which can be a big advantage for some users.  
  
RGB to RGBW conversion is calibrated for the neutral white channel BTF SK6812 but it can be easily changed (for cool and warm temperature) in the code. Search for "color calibration". In HyperHDR use "1.5" gamma for red, blue and green for best effect in the "Image Processing" tab.


| LED strip / Device             | HyperSerialESP32 |
|--------------------------------|:----------------:|
| SK6812 cold white              |       yes        |
| SK6812 neutral white           |       yes        |
| WS281x                         |       yes        |
| SPI (APA102, SK9812, HD107...) |       yes        |
  
# Data integrity check
  
Why the data integrity check was introduced which causes incompatibility with other software? Because at 2Mb speed many chip-makers allow few percent error in the transmission. And we do not want to have any distracting flashes. Broken frames are abandon without showing them. At 100Hz for 250 leds approximately 1-5% of the frames are broken.
  
# Flashing
  
For **RGBW LED strip** like RGBW SK6812 NEUTRAL white choose: *HyperSerialESP32_SK6812_NEUTRAL_WHITE_FIRSTLED_xxxxxx.bin*  
  
For **RGBW LED strip** like RGBW SK6812 COLD white choose: *HyperSerialESP32_SK6812_COLD_WHITE_FIRSTLED_xxxxxx.bin*  
  
For **RGB LED strip** like WS8212b or WS8213 choose: *HyperSerialESP32_WS821x_FIRSTLED_xxxxxx.bin*  
  
For **SPI driven RGB LED strip** APA102: *HyperSerialESP32_APA102_FIRSTLED_xxxxxx.bin*, WS8201: *HyperSerialESP32_WS8201_rbg_FIRSTLED_xxxxxx.bin*  
  
If the first LED in the strip should be enabled or set to black is your choice.  
For the RGBW firmware the white channel is automatically calculated and R,G,B channels are corrected.  
esphome-flasher is recommended.  
  
# Usage in HyperHDR
  
Make sure you set "Refresh time" to zero, "Baudrate" to 2000000 and enabled HyperHDR's AWA protocol.  
Enabling "White channel calibration" is optional, if you want to fine tune the white channel balance of your sk6812 RGBW LED strip.  

![obraz](https://user-images.githubusercontent.com/69086569/192893595-324cfcf8-e247-438c-88ce-e52a29463121.png)
   
# Compiling
  
Compile the sketch using Arduino IDE. You need:  
- https://github.com/espressif/arduino-esp32 (boards for ESP32)  
- Makuna/NeoPixelBus (install from Arduino IDE: manage libraries)  
  
**Options (first lines of the sketch):**  
  
For RGB strip like WS8212b delete it or comment it with '//', leave it for RGBW SK6812:  
*#define   THIS_IS_RGBW*  
  
For RGBW cold white LED strip version if above declaration is defined, delete it or comment it with '//' for RGBW neutral:  
*#define   COLD_WHITE*  
  
SPI: for APA102/SK9822/HD107 delete it or comment it with '//', leave it for WS2801:  
*#define   is_WS2801*
  
Skip first led in the strip, that is used as level shifter:  
*bool      skipFirstLed = true;*  
  
Serial port speed:  
*int       serialSpeed = 2000000;*  
  
Don't change LED's count as it is dynamic.  
  
# Pinout
  
**ESP32:**  
**LED output (non-SPI):** GPIO 2  
**LED output (SPI):** GPIO 0 for Clock, GPIO 2 for Data  
  
# Disclaimer
  
You use it on your own risk.  
Don't touch these firmwares if you don't know how to put the device in the programming mode if something goes wrong.  
As per the MIT license, I assume no liability for any damage to you or any other person or equipment.  
