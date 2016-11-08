# Troubleshooting


----------


## Steps in case of a suspected DNS error culprit

### Zone files

Zone files are very sensitive to faulty syntax.

Hostnames always need to be closed by a "."

>example pu001.linuxlab.net.

IP Addresses are noted differently in the zone files.

"in-addr.arpa." is added and the dotted notation without the host bits is reversed.

> example 192.0.2.0/24 becomes 2.0.192.in-addr-arpa.

#### Checking Zone files for syntax errors

For the main config file /etc/named.conf type:

	$ sudo named-checkconf /etc/named.conf

For Zone Files type:

    $ sudo named-checkzone linuxlab.lan /var/named/linuxlab.lan
    $ sudo named-checkzone 15.168.192.in-addr.arpa \ /var/named/15.168.192.in-addr.arpa

To see live error messages, open a new bash console and log in to the server, type:

	$ sudo journalctl -l -f -u named.service

Now open a new console and restart the service, you will see all information about the errors live in the first console.

## Common errors

1. File permissions: The user needs to be given read or write access
2. Samba Configuration: The share has give the correct access rights to the user through config-file /etc/samba/smb.conf
3. SELinux: The directory needs the correct SELinux context.

If one of these 3 elements are too strictly configured, users won't have the requested access.

### Checking Samba config file

	testparm -s 

### Checking Samba error messages

Give a summary of the shares on server FILES
	
	smbclient -L //files/

Log in on a share as user lizae with pw letmein

	smbclient //files/public/ -Ulizae%letmein

Log in to share as guest

	smbclient //files/public -U%

## Troubleshooting with TCP/IP Stack model

It's best to start on the bottom of the stack and work your way up to troubleshoot. This is bottom-up troubleshooting

1. Datalink layer: Cables, networkports on switch/router, networkcard...
2. Internet layer: IP address config, Default Gateway, DNS-Server
3. Transport layer: State of the networkservice, open ports, firewall settings
4. Application layer: faulty config files, logs, availability of service from the network...


### Datalink Layer Troubleshooting

* Is the power on?
* Is the network cable plugged in?
	* In VirtualBox, check the Network settings and see if "Cable connected" is checked
* Are the Ethernet port LEDs on both the machine and switch?
	* If not, check cable
	* Check what different colors and frequencies mean on your switch
* Use the command ip link
	* UP: interface is connected
	* NO-CARRIER: no signal on the interface

### Network Layer Troubleshooting

Each host needs to have an **IP address** assigned, a set **default gateway** and a **DNS server**. Check local settings before reaching out of the network.

#### IP address

The address may have been set manually or automatically through DHCP. To check this, go in to `/etc/sysconfig/network-scripts/ifcfg-IFACE` with IFACE being the name of the network interface.

To get a list of the IP addresses for each interface type

	ip a

You should know the expected IP addresses, or the range or network address and mask.

* Many home routers are set to a range of 192.168.0.0/24, 192.168.1.0/24
* On a VirtualBox NAT interface, the IP should be 10.0.2.15/8
* On a VirtualBox host-only int, the ip of the VM depends on how you configd the VM. Default this is 192.168.56.0/24, with DHCP this starts at 192.168.56.101

##### Possible errors and causes with DHCP

* No IP
	* DHCP can't be reached
	* DHCP server won't give IP to host
* IP looking like 169.254.x.x
	* DHCP couldn't be reached and link-local address was given
* IP not in expected range
	* could've set a fixed IP and forgot to set to DHCP :/

##### Possible errors and causes with manual settings

* Ip not in expected range
	* Mistake in IP address setup, check config file
* Correct IP, but network unreachable
	* Check the network mask, this should be the same for all hosts on the lan

#### Default Gateway

Network traffic that goes outside the network passes through the default gateway (the router). Every host on the LAN should know this default gateway for traffic.

	ip route

##### Possible errors and causes DHCP

* No default gateway set
	* DHCP server could be configd badly
	* prolly also a mistake in IP address of this host, check and fix
* Unexpected default gateway address
	* You may have manually fixed the IP and forgot to turn on DHCP
	* DHCP server could be configd badly

#### DNS server

Every host should be able to contact a DNS server. Check the file `/etc/resolv.conf`, it usually has a header that shows if it was generated automatically and should have lines starting with nameserver.

Do the same troubleshooting of the Default Gateway.

##### Check LAN connection

If everything up to this point wasn't the issue or is fixed, check if the hosts on the LAN can be reached.

* Ping the default gateway
* Ping another host on the LAN
* Check DNS name resolution: `dig www.google.com`

> Take note that ping or traceroute commands can sometimes be blocked as some sysadmins block ICMP traffic on routers.

> pinging google.com is not immediatly suitable as it depends on perfect host network settings, routing, DNS and no blocking of ICMP

### Transport Layer Troubleshooting

Here we check whether or not the network service is running, what port it uses and if the firewall allows traffic on the port.

> Example is given with httpd, but can be applied to all

#### Service and port

* Is the service running? 

	sudo systemctl status httpd.service
	
If the output is `active (running)` you're gucci, if its `inactive (dead)`, start it up and make sure it starts automatically on startup. To do this, run:

	sudo systemctl start httpd.service
	sudo systemctl enable httpd.service

* What port is the service using?

	sudo ss -tlnp // -t = list TCP,  -l = server, -n = port numbers, -p = processes behind them, this last on requires sudo

Output can depend on config. Usually httpd listens on port 80 (HTTP) or 443 (HTTPS), but both may have been set to a non-standard. Check `/etc/services` for standard port numbers for well-known network services.

#### Firewall Setting

* Does the firewall allow traffic on the service?

	sudo firewall-cmd --list-all

In the output, check if the interface the service listens to is listed. Check if the name of the service is listed too.

The service you use should be listed in

	firewall-cmd --get-services

> The service name for firwalld is not necessarily the same for systemd. For example BINd is called named.service by systemd while firewalld calls it dns. Check this if necessary for your service.

### Application Layer Troubleshooting

Here we check if the application is configd correctly and if its available to clients and responds correctly to requests.

#### Log files

Check the log using

	sudo journalctl -f -u // relevant service can be added after this, for example httpd.service

or by looking at the log file at `/var/log`

#### Config Files

Check the config file somewhere in `/etc/` for example `/etc/httpd/httpd.conf`. First create a backup of the default one if possible.

* Check the config file for errors, check documentation for this
* validate the syntax, most commands have a command for this
	* for example for httpd: apachectl configtest
* After making changes, restart the service
	* `sudo systemctl restart httpd.service`

#### Availability

You can check availability on the loopback interface but its necessary to repeat this from another host. The loopback is not firewalled while the rest are.

* Do a port scan from another host on the lan, e.g.
	* `sudo nmap -sS -p 80,443 HOST // perform a TCP SYN scan on port 80 and 443`
* Use a test tool or client software to check availability of the service
	* `wget http://HOST/, wget https://HOST/`
	* `curl http://HOST/, curl https://HOST/`


## Sources

[Bert Van Vreckems Troubleshooting guide](https://github.com/bertvv/cheat-sheets/blob/master/print/NetworkTroubleshooting.pdf) + [Syllabus Enterprise linux](https://chamilo.hogent.be/Chamilo/Libraries/Resources/Javascript/Plugin/PDFJS/web/viewer.html?file=https%3A%2F%2Fchamilo.hogent.be%2Findex.php%3Fapplication%3DChamilo%255CCore%255CRepository%26go%3DDocumentDownloader%26object%3D2327320%26security_code%3D24ed8910204771c621e8dae1f7cbad0e2148b8bc%26display%3D1)

