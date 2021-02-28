# Connect the Raspberry Pi to the internet via GSM

![RaspberryPi Diagram](https://github.com/Instituto-Iracema/documentation/blob/main/Firmware/src/gpio_raspberry4.png?raw=true)

Install PPP

```sh
$ sudo apt-get update
$ sudo apt-get install ppp
```

First, we will create the PPP configuration file.
	
$ nano /etc/ppp/options

```sh
auth
crtscts
lock
hide-password
modem
mru 296
mtu 296
lcp-echo-interval 30
lcp-echo-failure 4
noipx
persist
asyncmap 0xa0000
mru 1500
refuse-chap
ipcp-max-failure 30
logfile /home/root/ppp
```


In this case we will use the PAP authentication file (they vary in relation to the provider).
```sh
$ nano /etc/ppp/pap-secrets
```
Modify this file according to your preferences.
```sh
#    *      password
tim tim tim
```

Now, you should now create the file inside the /etc/ppp/peer folder. Here will be the settings for each operator and the path to the chat file.

```sh
$ nano /etc/ppp/peers/tim-3g.provider
```

```
hide-password
noauth
debug
defaultroute
noipdefault
user vivo
remotename vivo
ipparam vivo
persist
usepeerdns
/dev/ttyUSB0 115200 crtscts
#replacedefaultroute -- Check it later
connect 'chat -v -f /etc/ppp/chat/tim-3g.chat'
```

Now we are going to create the file responsible for sending AT commands to the 3G module to establish the internet connection. In this case I'm using the SIM800L module. 

```
ECHO
ON
ABORT
'BUSY'
ABORT
'NO CARRIER'
ABORT
'ERROR'
""
ATZ OK
\d\dAT+CGDCONT=1,"IP","timbrasil.br" OK
\d\d\dATDT*99# CONNECT
```
In this case, I'm using TIM as an internet provider. So I'm going to put TIM's APN which is timbrasil.br.

```After these steps it is recommended to restart the Raspberry.```

Now it's finally time to test whether everything worked. To do this, simply execute the command below.
```
$ pon tim-3g.provider
```


To make sure it really worked, check if the PPP0 interface is online using the ```$ ifconfig```.command and then ```ping -I ppp0 google.com```.
