# rak_common_for_gateway

##	Introduction 

The aim of this project is to help users to use the RAK Raspberry Pi Developer Gateway more easily. The User Guide can be get from our Web(https://docs.rakwireless.com/Introduction/).

--------------------------------------------------------------------------

*** EDITED for work with Pine64 Gateway - work in progress more information/instruction soon *** -RTP

### Pine64 Gateway Setup

*NOTE: my goal was basically at this point to get the concentrator started/GPS working. If you notice changes needed
please do post an issue/submit or fork something! Thanks! :)

General Steps:

REQUIREMENTS:

*** LoRa  + GPS antenna (preferably outside or antenna by the window- without GPS it will not show up on TTN); 
***Very Important*** to have antennas connected before attempting to setup or you could DAMAGE your Gateway ***

1.) Download SOPINE/LTS distribution of choice (Armbian used in example below)
For Pine64 Gateway select a SOPINE/LTS compatible

2.) After installing an LTS/SOPINE compatible image to sdcard/emmc, boot up and clone this fork by issuing:

clone https://github.com/RightToPrivacy/rak_common_for_gateway.git

3.) Now issue: sudo armbian-config -> System and enable UART/SPI/I2C (Concentrator/GPS/Temp sensor)

4.) Make sure /boot/armbianEnv.txt has this line in it: param_spidev_spi_bus=0

5.) Reboot for changes to take effect.  

6.) cd into your cloned rak_common_for_gateway directory (cloned from this fork from step #2)

7.) Follow rak_common_for_gateway instructions

8.) After installing everything open the command: sudo gateway-config

9.) inside gateway-config go to packet forwarder config editing

10.) Verify SPI device (concentrator) says /dev/spidev0.0 and not something else (installation of option #7 (Pine64 Gateway) should set this for you)

11.) make sure the GPS device is /dev/ttyS2 (Installation of option #7 (Pine64 Gateway) should set this for you)

12.) Save the Packet Forwarder config and change the LoRa channel config to either TTN (thethingsnetwork or Other server)

13.) Select your country's LoRa location/frequency

14.) Restart Packet Forwarder and quit

15.) Check the status by viewing journalctl or systemctl status ttn-gateway; if not active, check journalctl to see what failed and its reasoning

16.) You may need to enter gateway-config again and re-enter the devices in case a 3rd party package changes them (something I noticed and have not edited atm)

17.) Once GPS registers reliable signal (usually a few min after starting) you should see your Gateway on TTN map and tcpdump|grep 1700 should show valid radius packets. 

You should also see the Gateway version + EUI and other information on starting the concentrator, without any errors.. If you see errors, check troubleshooting below. I have examples of journalctl log at:
https://forum.pine64.org/showthread.php?tid=13682&highlight=pinedio

[ will edit more soon please check back and feel free to add/fix anything that you notice to help us get Pine64 Gateway running properly! :) ]

***TROUBLESHOOTING:

The Things Network Gateway Commands (SSH/Shell access):
Check the status of ttn-gateway (if selecting The Things Network Channel) service by issuing:
systemctl status ttn-gateway
Restart TTN Gateway:
systemctl restart ttn-gateway
Stop TTN Gateway:
systemctl stop ttn-gateway
Start TTN Gateway:
systemctl start ttn-gateway

Check gateway version/ID
gateway-version

Configuration:
sudo gateway-config

***Gateway failed to start concentrator? 
- Check packet forwarder config to ensure device location is correct: sudo gateway-config ---> edit packet-forwarder config 
* Ensure the SX120x_conf location in packet-forwarder config matches:

        "com_type": "SPI",
        "com_path": "/dev/spidev0.0",
	
* Ensure GPS matches /dev/ttyS2:
	
        "gps_tty_path": "/dev/ttyS2",

(anytime you edit packet-forwarder config, make sure to then restart packet forwarder)

***GPS not registering location? 
- Consider running GPS antenna closer to a window (if indoors). GPS needs a direct line of sight. To test out GPS try looking at gpsmon or cat /dev/ttyS2
(ensuring GPS functions before expecting gateway to read it is recommended)

- See if you have GPS location information by running: cat /dev/ttyS2 or gpsmon (thanks JF for mentioning this- helped me)

If you have more tips/troubleshooting/information, please do send: either as an issue, fork, mod. Or optionally email: righttoprivacy[at]tutanota.com

Thanks! :)

[/END EDIT PINE64 GATEWAY SETUP START]
------------------------------------------------------------------

##	Supported platforms

This project currently provides support for the below platforms.
* RAK831(Choose RAK2245)
* RAK2245
* RAK7243/RAK7244 no LTE
* RAK7243/RAK7244 with LTE
* RAK833(USB)(Choose RAK2247 USB)
* RAK2247(USB)
* RAK833(SPI)(Choose RAK2247 SPI)
* RAK2247(SPI)
* RAK2246
* RAK7248 no LTE (RAK2287 + Pine64)
* RAK7248 with LTE (RAK2287 + LTE + raspberry pi)

##	Changelog
2021-03-09 V4.2.8

* 1.Added support for RAK2287-USB.
* 2.Remove some obsolete models.

2021-01-21 V4.2.7

* 1.Only upgraded for RAK2287, added EU433 and CN470 for RAK2287.

2020-11-25 V4.2.6

* 1.Added support for RAK7248C.
* 2.Added support for class B in global_conf.json, but it is disabled by default. When needed, you can enable it in `/opt/ttn-gateway/packet_forwarder/lora_pkt_fwd/global_conf.json`.

2020-09-01 V4.2.5

* 1.Add AS920_923.
* 2.EU433 changed to 8 consecutive channels.

2020-07-20 V4.2.4

* 1.Add other region global_conf.json for RAK2287.
* 2.Modify sx1302 tx power param.
* 3.Add RAK2285 support.

2020-07-09 V4.2.3

* 1.Some naming changes.

2020-06-28 V4.2.2

* 1.Fix a display issue with gateway-config.
* 2.Delete the temperature printing of RAK2287.

2020-05-02 V4.2.1

* 1.Added support for RAK2287 spi version.

2020-02-14 V4.2.0

* 1.Added support for RAK2246 spi version.
* 2.Fix the bug that the internet cannot be accessed after ppp0 redial.
* 3.install.sh can pass --help parameter to see more installation information.
* 4.Install the latest version of chirpstack by default.
* 5.gateway-version print more information.
* 6.gateway-config and gateway-version show the actual gateway_id.
* 7.Create a rak_ap file in the /boot directory to restore to ap mode.
* 8.Modify the global_conf.json file of 7246 to make the transmit power more accurate.
* 9.When there is no match for tx power, the nearest smaller power will be used.[Semtech UDP (legacy) packet forwarder](https://github.com/Lora-net/packet_forwarder/blob/master/lora_pkt_fwd/src/lora_pkt_fwd.c#L2517-L2527) 
* 10.Delete default DNS(8.8.8.8, 223.5.5.5), the gateway will use the DNS assigned by the router to which it is connected.
* 11.In case of GPS connection, automatically change the system time to GPS time.

2019-12-17 V4.1.1
* Added support for rak2247/rak833 spi version.

2019-12-02
* Fix a bug. There is a mistake word "diable" in rak/gateway-config script, line 272, it should be do_ChirpStack disable.

2019-11-19 V4.1.0
* 1.LoRaServer changed its name to ChirpStack.
* 2.ChirpStack turns off ADR by default.
* 3.Unconfigure the ip of eth0 to 192.168.10.10.
* 4.Users can change the gateway_id in local_conf.json.
* 5.Increase i2c rate for rak7243.
* 6.Install chirpstack only on Raspberry Pi 3 and Raspberry Pi 4. Pi zero can't run ChirpStack.
* 7.In the AP mode, the gateway IP address is 192.168.230.1.

2019-09-19 V4.0.0
* Use lan0's mac address when eth0 does not exist.

2019-05-24 V2.9

* 1.Multiple models are integrated with one common version.
* 2.Upgrade LoRa server to 3.0.

##	Installation procedure

step1 : Download and install [Raspbian Buster LITE](https://www.raspberrypi.org/downloads/raspbian/) 

step2 : Use "sudo raspi-config" command, enable spi and i2c interface, disable login shell over serial and enable serial port hardware.

step3 : Clone the installer and start the installation (More installation options can be found in "sudo ./install.sh --help").

      $ sudo apt update; sudo apt install git -y
      $ git clone https://github.com/RAKWireless/rak_common_for_gateway.git ~/rak_common_for_gateway
      $ cd ~/rak_common_for_gateway
      $ sudo ./install.sh

step4 : Next you will see some messages as follow. Please select the corresponding hardware model.

      Please select your gateway model:
      *	 1.RAK2245
      *	 2.RAK7243/RAK7244 no LTE
      *	 3.RAK7243/RAK7244 with LTE
      *	 4.RAK2247(USB)
      *	 5.RAK2247(SPI)
      *	 6.RAK2246
      *	 7.RAK7248 no LTE (RAK2287 SPI + raspberry pi)
      *	 8.RAK7248 with LTE (RAK2287 SPI + LTE + raspberry pi)
	  *	 9.RAK2287 USB
      Please enter 1-9 to select the model:

step5 : Wait a moment and the installation is complete.

step6 : For more other features, please use "sudo gateway-config".

