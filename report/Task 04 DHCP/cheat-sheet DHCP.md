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

## Add Samba/Vsftpd roles to pr011 in site.yml
open site.yml in notepad ++

under the new host pr011, under roles, Add:

	- hosts: pr001
  	become: true
  	roles:
	    - bertvv.rh-base
	    - bertvv.dhcp


> make sure there are 2 spaces infront of any added role

This role has to be downloaded first under the folder /C/Users/Siebert/Documents/elnx-sme-SiebertT/ansible/roles.

## Downloading a role
For Windows, download the scripts from a GitHub Repository as a .zip and place them in the roles folder in the ansible folder.

now run the `roles-deps.sh` script from the directory of the project (Users/Documents/elnx-sme-SiebertT)

You can find the DHCP role [here](https://github.com/bertvv/ansible-role-dhcp)

## Add pr001 to the vagrant hosts file

Navigate to the `vagrant-hosts.yml`

Add:

	- name: pr001
	  ip: 172.16.0.2

## Add pr011 to the hosts_vars file

Navigate to the `host_vars` folder in Ansible folder.

Create a new .yml file named pr001.yml. Our configuration will go here.

## Configure pr011.yml
In this Ansible configuration file we will be configuring the DHCP role to our needs.

### Subnet Calculation

#### SUBNET 0
**RANGE**: 172.16.0.2 -> 172.16.127.254

**MASK**: 255.255.128.0

**NETWORK**:172.16.0.0
**BROAD**:172.16.127.255

#### SUBNET 1
**RANGE**:172.16.128.1 -> 172.16.191.254

**MASK**: 255.255.192.0

**NETWORK**: 172.16.128.0
**BROAD**: 172.16.191.255

1111 1111 . 1111 1111 . 11|00 0000 . 0000 0000

2^6 = 64

#### SUBNET 2

**RANGE**: 172.16.192.1 -> 172.16.255.253 ! RANGE KLEINER DAN SUBNET

**MASK**: 255.255.192.0

**NETWORK**: 172.16.192.0
**BROAD**: 172.16.255.255 MAAR GATEWAY is 172.16.255.254


#### IN MACHINE:

enp0s3: 10.0.2.15/24
enp0s8: 172.16.0.2/24

### pr001.yml config

```
dhcp_subnets:

  - ip: 172.16.0.0 // top subnet
    netmask: 255.255.0.0
    domain_name_servers:
          - 192.0.2.10
          - 192.0.2.11
    range_begin: 172.16.192.1 // dynamic range
    range_end: 172.16.255.253 // dynamic range
    max_lease_time: 14400
    routers: 172.16.255.254
    hosts: // excluded fixed hosts
    - name: pu001
      mac: '08:00:27:16:C2:6C'
      ip: 192.0.2.10
    - name: pu002
      mac: '08:00:27:54:01:0E'
      ip: 192.0.2.11
    - name: pu004
      mac: '08:00:27:0C:36:09'
      ip: 192.0.2.50
    - name: pr011
      mac: '08:00:27:DE:8D:BB'
      ip: 172.16.0.11
      pools:
          -  max_lease_time: 43200
             range_begin: 172.16.128.1
             range_end: 172.16.255.253



dhcp_global_domain_name: avalon.lan
dhcp_global_domain_name_servers: // DNS
      - 8.8.8.8
      - 8.8.4.4
dhcp_global_routers: 192.0.2.254 //gateway

```
