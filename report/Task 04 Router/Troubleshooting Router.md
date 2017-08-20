# Troubleshooting Router

## Router problems

### Gateway setup

A router that acts as a DNS forwarder should not have a default gateway set, this will forward all the traffic meant for outside will be rerouted to the router itself, this means all traffic gets stopped.

> Solution, don't set a Default Gateway for a DNS forwarding Router

### NAT Translations

NAT is only required when you want to hide certain IP addresses, in this case the internal ones, from the public internet.

In a DMZ or Demilitarized zone, there is no need for this security measure as it is public and has public IP addresses.

In this case, NAT should only be applied to the internal network, as this is to be shielded from outside.

> Solution: Make a drawing of the network setup, this will clarify a lot about how and where the internet should be translated and routed to.

### DNS
In the case of this Assignment, the internet is provided by an 'ISP' on a WAN link interface connected to the router.

Instead of using Google DNS servers, the DNS servers that were made in earlier parts of the assignment can be used. The DNS server of the 'ISP' is provided as well.

It is important to distinguish the local domain DNS and the DNS server required to reach the public internet.

These should be set seperately in the router.

```

set service dns forwarding domain avalon.lan server 192.0.2.10
set service dns forwarding name-server 10.0.2.3

```

> Check the requirements, distinguish between a local DNS and public DNS.

## Troubleshooting commands for Router

### Show IP route
The `show IP route` command can be used to **check the Default Gateway**.


    ```
    vagrant@router:~$ show ip route
    Codes: K - kernel route, C - connected, S - static, R - RIP, O - OSPF,
           I - ISIS, B - BGP, > - selected route, * - FIB route

    S>* 0.0.0.0/0 [210/0] via **10.0.2.2, eth0**
    C>* 10.0.2.0/24 is directly connected, eth0
    C>* 127.0.0.0/8 is directly connected, lo
    C>* 172.16.0.0/16 is directly connected, eth2
    C>* 192.0.2.0/24 is directly connected, eth1
    ```

In this scenario we can see that 0.0.0.0/0, e.g. the default route is connected via 10.0.2.2 on the eth0 interface. This interface is connected to the ISP's internet, this is correct.


### dig
The `dig` command can be used to see if the** master and slave DNS servers can communicate with eachother properly**.

```
    [vagrant@pu001 ~]$ dig @192.0.2.10 www.avalon.lan +short
    pu004.avalon.lan.
    192.0.2.50
    [vagrant@pu001 ~]$ dig @192.0.2.11 www.avalon.lan +short
    pu004.avalon.lan.
    192.0.2.50

```

In this case, 192.0.2.10 is the master DNS while 192.0.2.11 is the slave DNS. The output proves this communication is intact. They both recognise that LAMP server pu004 has the IP address 192.0.2.50

The command can also be used to test if** hosts in the internal network can communicate through the DNS servers**.

            ```
            [vagrant@pr001 ~]$ dig @192.0.2.10 www.avalon.lan +short
            pu004.avalon.lan.
            192.0.2.50
            [vagrant@pr001 ~]$ dig @192.0.2.11 www.avalon.lan +short
            pu004.avalon.lan.
            192.0.2.50
            ```

In this scenario, the dig command is executed from the DHCP server, it tries to communicate with pu004 of the avalon.lan domain through the master and slave DNS servers.

### Curl
The `curl` command can be used as a** browser tool in the CLI**.

Usually, it returns HTML code that is displayed on the entered page.

You can use a website such as ![](icanhazip.com) to simply get an IP address returned.

This can be really useful when there is no GUI immediatly available.

### nslookup
The command `nslookup` can be used to **check if a certain host can make use of DNS services**.

In the scenario below, nslookup is used on the router to check the internal and external network.

       ```
      vagrant@router:~$ nslookup www.hogent.be
      Server:    10.0.2.3
      Address 1: 10.0.2.3

      Name:      www.hogent.be
      Address 1: 178.62.144.90
      Address 2: 2a03:b0c0:0:1010::c7:7001
      vagrant@router:~$ nslookup www.avalon.lan 192.0.2.10
      Server:    192.0.2.10
      Address 1: 192.0.2.10

      nslookup: can't resolve 'www.avalon.lan'
      vagrant@router:~$ nslookup www.avalon.lan 192.0.2.11
      Server:    192.0.2.11
      Address 1: 192.0.2.11

      nslookup: can't resolve 'www.avalon.lan'
      ```

When looking up an external network such as hogent.be, we notice in the output that the DNS server provided by the ISP is used.

When trying to reach avalon.lan, the internal domain, through the DNS servers from the DMZ, it fails.

This may be caused by certain settings or firewalls not allowing this kind of querying on the DNS servers.
