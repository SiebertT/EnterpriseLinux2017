# Cheat sheets and checklists DHCP task 04

- Student name: [Siebert Timmermans](https://github.com/SiebertT)
- Github repo: [elnx-sme-SiebertT](https://github.com/HoGentTIN/elnx-sme-SiebertT)

## Basic commands

| Task                | Command |
| :---                | :---    |
| Query IP-adress(es) | `ip a`  |

## Git workflow

Simple workflow for a personal project without other contributors:

| Task                                         | Command                   |
| :---                                         | :---                      |
| Current project status                       | `git status`              |
| Select files to be committed                 | `git add FILE...`         |
| Commit changes to local repository           | `git commit -m 'MESSAGE'` |
| Push local changes to remote repository      | `git push`                |
| Pull changes from remote repository to local | `git pull`                |

## Checklist network configuration

1. Is the IP-adress correct? `ip a`
2. Is the router/default gateway correct? `ip r -n`
3. Is a DNS-server available? `cat /etc/resolv.conf`

## Add DHCP role to pr001 in site.yml
open site.yml in notepad ++ or your text editor

For the new host, pr001,  Add:

	- hosts: pr001
	  become: true
	  roles:
	    - bertvv.rh-base
	    - bertvv.dhcp


> make sure there are 2 spaces infront of any added role

This role has to be downloaded first under the folder _.../ansible/roles_

## Downloading a role
For Windows, download the scripts from a GitHub Repository as a .zip and place them in the roles folder in the ansible folder.

now run the `roles-deps.sh` script from the directory of the project through your CLI (Users/Documents/elnx-sme-SiebertT)

You can find the DHCP role [here](https://github.com/bertvv/ansible-role-dhcp)

## Add pr001 to the vagrant hosts file

Navigate to the `vagrant-hosts.yml`

Add:
	- name: pr001
	  ip: 172.16.0.2
	  netmask: 255.255.0.0

For the DHCP server, the netmask needs to be clarified manually, for the servers before this, this was default.

## Add pr011 to the hosts_vars file

Navigate to the `host_vars` folder in Ansible folder.

Create a new .yml file named pr001.yml. Our configuration will go here.

## Configure pr001.yml
In this Ansible configuration file we will be configuring the DHCP role to our needs.

### pr001.yml config

```
dhcp_subnets:

rhbase_install_packages: // installs the DHCP package
  - dhcp

rhbase_firewall_allow_services: // allows the DHCP services through the firewall
  - dhcp

dhcp_global_domain_name: avalon.lan  // specifies the global domain name as 'avalon.lan'

dhcp_global_domain_name_servers: // specifies the global DNS serves by IP, this is to access the internet outside of the network, for this we use the Google DNSes
  - 8.8.8.8
  - 8.8.4.4

dhcp_subnets: // specifies the subnets available to the DHCP server
  - ip: 172.16.0.0
    netmask: 255.255.0.0 // range of 172.16.0.1 -> 172.16.255.254 overall
	  domain_name_servers: // DNS servers for the internal network, pu001 and pu002
	    - 192.0.2.10
	    - 192.0.2.11
    max_lease_time: 43200 // lease time for provided IPs
    pools:
      - default_lease_time: 14400
        range_begin: 172.16.192.1
        range_end: 172.16.255.253
        allow: unknown-clients


dhcp_hosts: // pinpoints the static DHCP allocation for the specified interface
  - name: cl1
    mac: '08:00:27:85:03:66'
    ip: 172.16.128.2


```

## Set up test environment with VirtualBox

1. Set up a Linux distribution by adding an .ISO through the settings. In this case, a Fedora distribution is used
2. Navigate to the network settings of your newly created VM
3. Make Adapter 1 and Adapter 2 host only, so they can receive DHCP addresses
4. Click the 'Advanced' dropdown menu on one of the adapters. Copy paste the MAC address there.
5. In the pr001.yml file, paste down the MAC address at the static host allocation. Mind the `:`'s inbetween every 2 characters.
6. Enter your newly set up VM, check out the IP addresses provided either through CLI by using `ip a` or through the GUI. It should be the static address 172.16.128.2 and a dynamic one that is between the range.
