# rpi-audio-hat
Raspberry Pi PCM1808 and 5102 audio hat for SDR tranceiver

##Hardware
Kicad files and gerbers are availeble in hardware directory



## How compile and install driver for pcm1808 and pcm5102
To support the audio card in slave mode an overlay need to be created. The raspberry pi supports already most audio cards with pcm1808 and pcm5102.
The procedures and files are taken from Akiyuki Okayasu github page https://github.com/AkiyukiOkayasu/RaspberryPi_I2S_Slave

Compile on Raspberry Pi  
```bash
dtc -@ -H epapr -O dtb -o genericstereoaudiocodec.dtbo -Wno-unit_address_vs_reg genericstereoaudiocodec.dts
```

Copy i2smaster.dtbo to /boot/overlays  
```bash
sudo cp genericstereoaudiocodec.dtbo /boot/overlays
```

Edit /boot/config.txt  
Enable I2S and add i2smaster device tree overlay  
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

