# Putting it all together: gateway & DHCP

## Learning goals

- Being able to set up a DHCP server for a small network
    - Understanding the configuration of DHCP and being able to adapt it for a specific situation
    - Assign basic network configuration settings to clients: IP address and subnet mask, default gateway, DNS servers
    - Setting up subnets and pools of dynamic IP addresses
    - Assign a reserved IP address based on MAC address
    - Configuring the lease time
- Being able to set up a router with VyOS
    - Configure network interfaces
    - Configure Network Address Translation
    - Set up a DNS forwarder

## Assignment

In this assignment, the goal is to complete the network we've been building with a DHCP server for workstations and a router. When a workstation is attached to the network, it should receive correct network settings from the DHCP server (IP address, default gateway, DNS) and be able to use the services on the local network (specifically the webserver and fileserver) and to access the Internet through the router.

### DHCP

Known hosts should receive a reserved IP address based on their MAC address (with a lease time of 12 hours). Hosts that do not have their MAC address registered should get a dynamic IP address (with a lease time of 4 hours). The IP range is subdivided as follows:

| Start          | End            | Host type                                |
| :---           | :---           | :---                                     |
| 172.16.0.1     | -              | VirtualBox host system                   |
| 172.16.0.2     | 172.16.127.254 | Hosts with static IP (servers)           |
| 172.16.128.1   | 172.16.191.254 | Hosts with reserved IP, based on MAC (*) |
| 172.16.192.1   | 172.16.255.253 | Hosts with dynamic IP (*)                |
| 172.16.255.254 | -              | Gateway                                  |

(*) Only these IP addresses are assigned by the DHCP server!

### Router

  For the router, we're going to use a specialized Linux distribution, [VyOS](http://vyos.net/). The Vagrant configuration for this host will be considerably different to the others. In order to add it to the Vagrant environment, add the following code to the Vagrantfile, *outside of the loop* `hosts.each do |host|`

```Ruby
  # VyOS Router
  config.vm.define 'router' do |router|
    router.vm.box = 'bertvv/vyos116'
    router.vm.network :private_network,
      ip: '192.0.2.254',
      netmask: '255.255.255.0',
      auto_config: false
    router.vm.network :private_network,
      ip: '172.16.255.254',
      netmask: '255.255.0.0',
      auto_config: false
    router.ssh.insert_key = false

    router.vm.provision "shell" do |sh|
      sh.path = "scripts/router-config.sh"
    end
  end
```

An overview of its interfaces:

| Interface | VBox adapter | IP address       | Remarks  |
| :---      | :---         | :---             | :---     |
| `eth0`    | NAT          | 10.0.2.15 (DHCP) | WAN link |
| `eth1`    | Host-only    | 192.0.2.254      | DMZ      |
| `eth2`    | Host-only    | 172.16.255.254   | internal |

- Configure the network interfaces, ensure `eth0` is used as WAN link. Assume that the network configuration settings on this interface was assigned by the Internet Service Provider of Avalon. Network traffic to the Internet should be forwarded to the correct IP address.
- This router is also configured as a *forwarding* DNS server. This means that it does not have its own zone definitions, bit it forwards all requests to appropriate name servers:
    - DNS requests for the `avalon.lan` domain are forwarded to the authoritative name server you set up in a previous assignment;
    - All other DNS requests are forwarded to the appropriate IP address (the DNS servers assigned by the "ISP").
- The internal network has a private IP range that should not be routed to the external network. Set up Network Address Translation for all traffic originating from the internal network.
- For synchronizing the system clock, most computer systems use NTP (Network Time Protocol). Delete the default NTP servers, and use the pool zone for your location (e.g. [be.pool.ntp.org](http://www.pool.ntp.org/zone/be) for Belgium). Also, set the time zone.

## Acceptance test

There are no automated tests for validating the DHCP server, so you need to use a manual procedure. A good test plan is important! Describe a detailed procedure with specific commands to use or actions to take in order to prove that the specifications are met, including expected results.

Some suggestions:

- Create a new VirtualBox VM manually, give it two host-only network interfaces, both attached to the VirtualBox host-only network with IP 172.16.0.0/16.
- Write down the MAC address of one of the two interfaces (or set it manually).
- Boot the VM with a LiveCD ISO (e.g. Fedora, but Ubuntu, Kali, etc. should also be fine).
- Ensure both interfaces will get an IP address assigned by DHCP
- Check the IP addresses: one should be the reserved IP address, the other should come from the pool of dynamic addresses.

Finally, this VM should be able to view the "company website" by surfing to <http://www.avalon.lan> in a web browser and be able to access the fileserver both through SMB and FTP.
