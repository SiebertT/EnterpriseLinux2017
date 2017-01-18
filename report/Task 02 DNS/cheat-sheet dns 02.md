# Cheat sheets and checklists DNS task 02

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

## Add the BIND role
open site.yml in notepad ++

Add


	- hosts: pu001
	  become: true
	  roles:
	    - bertvv.rh-base
	    - bertvv.bind

The DNS runs on host pu001, that is why we add this in the site.yml file. We configure that the roles need to be the rh-base and the bind role for DNS. Become is set to true for rights.

For the slave DNS, add:

	- hosts: pu002
	  become: true
	  roles:
	    - bertvv.rh-base
	    - bertvv.bind

> make sure there are 2 spaces infront of any added role

This role has to be downloaded first under the roles folder (/ansible/roles)

## Downloading a role
For Windows, download the scripts from a GitHub Repository as a .zip and place them in the roles folder in the ansible folder.

now run the `roles-deps.sh` script from the directory of the project (Users/Documents/elnx-sme-SiebertT)

You can find the BIND role [here](https://galaxy.ansible.com/bertvv/bind/)

## Adding pu001 and pu002 in host_vars

Add the .yml files named pu001 and pu002 in /ansible/host_vars. This is where we will configure the variables for the DNS role.

## Adding pu001 and pu002 to the main vagrant-hosts.yml

For the master DNS, Add:

	- name: pu001
	  ip: 192.0.2.10

For the slave DNS, Add:

	- name: pu002
	  ip: 192.0.2.11

This has to be set for when you `vagrant up`.

## Setting up the correct variables for Master DNS in pu001.yml

1. set up the **name of the bind zone**. In this case, avalon.lan
2. Add the **zone networks**, in this case these are 192.0.2 and 172.16.
2. set the **master server ip**, e.g. the ip of the server pu001. This is 192.0.2.10
3. **Allow the queries**. Put this to any or specifically to the traffic.
4. Make sure the **bind can listen to ipv4**. Set any to be sure.
5. set the **zone name servers**. These are pu001 and pu002.
6. Set the **zone for the mail server**.
7. **Add the hosts** according to your config
8. !**Allow the DNS service from the rhbase role**!

File should look like this:

![](https://i.gyazo.com/1fa58aa63a5f886a2781fd81938973a6.png)

![](https://i.gyazo.com/4337b156af5d41e605ae894e841f6ed8.png)

## Setting up the correct variables for Slave DNS in pu002.yml

1. Set the **zone name**
2. Set the **master DNS ip**
3. Set the **zone networks**
4. Allow **querying**
5. Allow **listening to ipv4**
6. Set the **zone name servers**
7. !**Allow the DNS service from the rhbase role**!

File should look like this:

> Note: Make sure the master DNS IPs match in both .ymls!

![](https://i.gyazo.com/f4edccdb07f99099271ae67d7292879b.png)
