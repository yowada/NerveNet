### Configuring raspberry pi as router

1. First, we need to do a basic install of raspbian into the raspberry pi.
2. After we have done basic installation, we can then open a terminal in the raspberry pi.
3. Install 2 software
```
sudo apt-get install dnsmasq hostapd netfilter-persistent iptables-persistent
```
4. In the above steps, we installed 2 packages, dnsmasq is a tool to configure dns and dhcp, whereas, hostapd is use to configure wireless hotspot in the raspberry pi.
5. Edit the file /etc/sysctl.d/routed-ap.conf
```
# Enable IPv4 routing
net.ipv4.ip_forward=1
```
6. Run the following commands
```
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo netfilter-persistent save
```
7. Now we configure static IP for WLAN0 Interface
8. Add the following to the end of /etc/dhcpcd.conf file
```conf
interface wlan0 
    static ip_address = 10.20.1.1/24
    nohook wpa_supplicant
```
9. Create backup of the default dnsmasq config file
```
mv /etc/dnsmasq.conf /etc/dnsmasq.conf.old
```
10. Add the following to the blank /etc/dnsmasq.conf file
```
interface=wlan0
dhcp-range=10.20.1.2,10.20.1.20,255.255.255.0,24h
domain=wlan
address=/gw.wlan/10.20.1.1
```
> The range 10.20.1.2 to 10.20.1.20 is assigned to devices connecting to the network
11. Run the command to ensure wifi radio is not blocked
```
sudo rfkill unblock wlan
```
12. Now we will configure the apd
```
vi /etc/hostapd/hostapd.conf
```
add the following
```
country_code=NP
interface=wlan0
ssid=SSID
hw_mode=g
channel=7
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=PWD
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
```
> Replace SSID with your wifi name
> Replace PWD with your wifi password (at least 8 letters)
13. Reboot the device
14. Wifi should be available
