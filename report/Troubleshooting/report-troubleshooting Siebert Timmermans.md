# Enterprise Linux Lab Report - Troubleshooting

- Student name: NAME
- Class/group: TIN-TI-3B (Gent), TIN2-TI-3B (Aalst), TIN-TILE (Afstandsleren) [weglaten wat niet past]

## Instructions

- Write a detailed report in the "Report" section below (in Dutch or English)
- Use correct Markdown! Use fenced code blocks for commands and their output, terminal transcripts, ...
- The different phases in the bottom-up troubleshooting process are described in their own subsections (heading of level 3, i.e. starting with `###`) with the name of the phase as title.
- Every step is described in detail:
    - describe what is being tested
    - give the command, including options and arguments, needed to execute the test, or the absolute path to the configuration file to be verified
    - give the expected output of the command or content of the configuration file (only the relevant parts are sufficient!)
    - if the actual output is different from the one expected, explain the cause and describe how you fixed this by giving the exact commands or necessary changes to configuration files
- In the section "End result", describe the final state of the service:
    - copy/paste a transcript of running the acceptance tests
    - describe the result of accessing the service from the host system
    - describe any error messages that still remain

## Report

### Phase 1: Data link layer troubleshooting

* Is the power on? **Yes**
* Is the network cable plugged in? **No**
	* In VirtualBox, check the Network settings and see if "Cable connected" is checked -> Wasn't the case, **fixed**
* Are the Ethernet port LEDs on both the machine and switch? **Not applicable on VM**
	* If not, check cable
	* Check what different colors and frequencies mean on your switch
* Use the command ip link
	* UP: interface is connected: **Both enp0s3 and enp0s8 are up and running**
	* NO-CARRIER: no signal on the interface **Not the case after checking if the cable was connected**

#### Summary Data Link Layer
Used commands:

	ip link

Expected output: 

	Interfaces are up and running, shouldn't see NO-CARRIER.

Steps taken to fix:

	In VirtualBox Network settings, checked whether or not the "Cable Connected" setting was checked. It was NOT so checked it to fix.



### Phase 2: Network Layer Troubleshooting

Each host needs IP address assigned, a set Default Gateway and DNS server.

#### IP Adresses

The address may have been set manually or automatically through DHCP. To check this, go in to /etc/sysconfig/network-scripts/ifcfg-IFACE with IFACE being the name of the network interface.

#### enp0s3

**Bootproto set to DHCP** in /etc/sysconfig/network-scripts/ifcfg-enp0s3

#### enp0s8

**No Bootproto set, requires manual config because IP address not set** in /etc/sysconfig/network-scripts/ifcfg-enp0s8

**FIX** use command

	sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s8

Check the **IPADDR** variable and change it to `IPADDR=192.168.56.42`

Below this, add `NETMASK=255.255.255.0`

> Enter editing mode in vi by pressing insert key. To save and quit type :x

Save the changes you made and quit vi.

> Notice DNS or Default Gateway isn't set either, this will be handled next in the bottom-up model

Use command:

	ip a

To check the interfaces and see if the IPv4 addresses are set.

We can see that **enp0s3 is correctly set** to ip address 10.0.2.15 since its the NAT interface.

After our modification, we can see that **enp0s8 has the correct ip address** (192.168.56.42) as well.


#### Default Gateway

Now we check and fix the Default Gateway that needs to be set up in order to reach outside of the network.

Use command:

	ip route

To see if a default gateway is set for our interfaces.

We can see that enp0s3 has a set Default Gateway, in this case 10.0.2.2

This is clearly missing for enp0s8 as we noted before.

To specifically gain more information about this interface, use command:

	nmcli dev show enp0s8

Here we can clearly see that the Default Gateway isn't set, we can see that the interface is correctly wired though. The IP address is correctly set as well.

To fix this issue, we will set the Default Gateway of our interface enp0s8 to 10.0.2.2 as well.

To do this use command

	sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s8

Add the variable `GATEWAY=10.0.2.2`

Save and exit vi

Reload the network by using

	/etc/init.d/network restart

Now run `nmcli dev show enp0s8` again to check if our issue was fixed.

#### DNS server

Here we check if the DNS is configured properly.

Use command:

	sudo vi /etc/resolv.conf

To check the header, we can see the **nameserver 10.0.2.3 and search hogent.be**

Navigate to the ifcfg file once again with command

	sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s8

Make sure `PEERDNS=no`, this way it can take the static DNS from nameserver 10.0.2.3 out of the `/etc/resolv.conf` file

To check the DNS, we need to use a command like `dig google.com`

This is a utility from the bind-utils package which isn't on the VM right now, use:

	sudo yum install bind-utils

Here we can see the DNS is correctly set up. 

You can see it used the DNS 10.0.2.3 and we received no errors with the dig command. You can check this in the output at `status:NOERROR`.

### Transport Layer Troubleshooting
Here we check if the network service is running, the port it uses and if the firewall allows traffic.

#### Service and port

* Is the service running? Use command

	sudo systemctl status nginx.service

Here we take note that the output is inactive(dead), the service isn't currently running. To fix this, use command

	sudo systemctl start nginx.service
	sudo systemctl enable nginx.service

Here we receive the error the the service startup failed because the control process exited with errors. We're asked to run the status command again or use journalctl -xe.

When we check the error log we can see that the nginxn.pem is missing and required.

There is a typo in the certificate name

To fix this, enter the config file at 

	sudo vi /etc/nginx/nginx.conf

Navigate to the SSL Certificate variables. Change it to /etc/pki/tls/certs/nginx.pem

There is currently no listener for 443, or the HTTPS port. We will have to add this manually

	server{
		listen 433 ssl;
		server_name _;
		ssl_certificate_key /etc/pki/tls/private/nginx.key;
		ssl_certificate /etc/pki/tls/certs/nginx.pem;



#### Firewall setup

The firewall needs to let the traffic on the ports and service pass.

To check the current traffic allowed, use

	sudo firewall-cmd --list-all

The used services should be listed in

	firewall-cmd --get-services

To do this for nginx, we need to add the services http, https and open the ports required. Follow the commands below:

	sudo firewall-cmd --add-service=http --permanent
	sudo firewall-cmd --add-service=https --permanent
	sudo firewall-cmd --add-port=443/tcp --permanent
	sudo firewall-cmd --add-port=80/tcp --permanent
	sudo firewall-cmd --reload

If we use the earlier commands to check the services, we can now see that the ports are openened and services are added.

### Application layer troubleshooting

Use a test tool or client software to check availability of the service


* wget http://HOST/, wget https://HOST/
* curl http://HOST/, curl https://HOST/

with https://192.168.56.42/ as host.



## End result



## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.

https://www.digicert.com/ssl-certificate-installation-nginx.htm

https://www.nginx.com/resources/wiki/start/topics/examples/full/

https://www.linode.com/docs/websites/nginx/how-to-configure-nginx

https://www.centos.org/forums/viewtopic.php?t=51343