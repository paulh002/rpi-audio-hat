# rpi-audio-hat
Raspberry Pi PCM1808 and 5102 audio hat for SDR tranceiver.
This board can be used with the Tayloe mixers in this repo https://github.com/paulh002/SoapyHifiBerry as alternative for the HifiBerry
The SoapyHifiBerry driver is used to supply IQ signals to the SDR software see sdrberry SDR software (https://github.com/paulh002/sdrberry)
Configure the SoapyHifiBerry config file with the correct audio board name and sample frequencey

To test the board:
- To check if board is loaded:
```
aplay -l 
arecord -l
```

- Reord and play:
```
arecord -D hw:0,1 -c 2 -f S24_LE -r 96000 test.wav
aplay -c2 -f S24_LE -r 96000 test.wav
```

## Hardware
Kicad files and gerbers are availeble in hardware directory.
R10 and R11 are used to switch between clock from the Pi or internal PLL of the pcm5102 (gnd is internal pll)
J5 exposes I2S for setting the mixer frequency of the si5351 on the Tayloe mixer board.

## Prototype
Picture shows prototype using cheep aliexpress adc and dac boards
To connect the boards you can use this table:

pcm 5102
| Board | GPIO | Pin |
| ----- | ---- | --- |
| SCK   |  4   |  12 | // or connect to gnd
| LCK   | 19   |  35 |
| BCK   | 18   |  12 |
| DIN   | 21   |  40 |
| 5V    | 5V   |   2 |
| FMT   | GND  |  19 |
| FLT   | GND  |  19 |
| XSMT  | 3v   |   1 |

pcm 1808
| Board| GPIO | Pin |
| ---- | ---- | --- |
| SCK  |  4   |  12 | // or connect to gnd
| LCK  | 19   |  35 |
| BCK  | 18   |  12 |
| Dout | 20   |  38 |
| MD0  | 3V   |   1 |
| MD1  | 3V   |   1 |
| 5V   | 5V   |   2 |
| 3V   | 3V   |   1 |
| SCLK |  4   |   7 |  // SCLK need a clock, this can be generated by raspberry pi (See PiClock) but better is to use an xtal oscillator 24.576 Mhz for 96 Khz or 49.125 to overclock to 196 Khz

![prototype](https://github.com/paulh002/rpi-audio-hat/blob/main/prototype.jpg)

## Kicad files
Kicad design is a pcb version of the audio board

![pcb](https://github.com/paulh002/rpi-audio-hat/blob/main/RPi_Hat.png)


## How compile and install driver for pcm1808 and pcm5102
To support the audio card in slave mode an overlay need to be created. The raspberry pi supports already most audio cards with pcm1808 and pcm5102.
The procedure and files are taken from Akiyuki Okayasu github page https://github.com/AkiyukiOkayasu/RaspberryPi_I2S_Slave

Compile on Raspberry Pi  
```bash
dtc -@ -H epapr -O dtb -o genericstereoaudiocodec.dtbo -Wno-unit_address_vs_reg genericstereoaudiocodec.dts
```

Copy genericstereoaudiocodec.dtbo to /boot/overlays  
```bash
sudo cp genericstereoaudiocodec.dtbo /boot/overlays
```

Edit /boot/config.txt  
Enable I2S and add genericstereoaudiocodec device tree overlay  
```/boot/config.txt    # Uncomment some or all of these to enable the optional hardware interface
#dtparam=i2c_arm=on
dtparam=i2s=on
#dtparam=spi=on
dtoverlay=genericstereoaudiocodec
```

If you don't need HDMI audio output and Raspberry Pi's headphone output, comment out "dtparam=audio=on" by hash.  
like this.  
```/boot/config.txt
#dtparam=audio=on
```

