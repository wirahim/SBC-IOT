# SBC-IOT

## PiHole-Unbound-PiVPN


1. Install PiHole from: 
https://pi-hole.net/
2. Install unbound and follow tutorial from:
https://docs.pi-hole.net/guides/dns/unbound/

After step 1 and 2, and after setting DNS server IP in my Mikrotik router I was having DNS loop which was causing excessing concurrent DNS request in PiHole. 

This link provided a solution: https://www.reddit.com/r/pihole/comments/rnoyw0/comment/hpts7o1/?utm_source=share&utm_medium=ios_app&utm_name=iossmf&context=3
```
sudo nano /etc/resolvconf.conf
```
go to bottom and find and delete 
```
unbound_conf=/etc/unbound/unbound.conf.d/resolvconf_resolvers.conf
```

now edit this file
```
sudo nano /etc/unbound/unbound.conf.d/resolvconf_resolvers.conf
```
replace everything inside with the following

```
forward-zone:
        name: "."
        forward-tls-upstream: yes
        forward-addr: 2606:4700:4700::1111@853
        forward-addr: 2606:4700:4700::1001@853
        forward-addr: 1.1.1.1@853
        forward-addr: 1.0.0.1@853
        forward-addr: 8.8.8.8@53
        forward-addr: 8.8.4.4@53
```
Restart unbound
```
sudo service unbound restart
```
This solved the concurrent DNS request issues with PiHole

3. Install and configure PiVPN

Now I was having 2 problems:

* 1: Devices can connect to vpn but can not access internet or local network. PiVPN somehow did not finish iptable setup. Running debug solved this issue.
```
sudo pivpn -d
```
* 2. the configs folder which holds the files required to create the tunnels were inaccessible. Take ownership of the folder and files.
```
sudo chown username:username -R configs
```