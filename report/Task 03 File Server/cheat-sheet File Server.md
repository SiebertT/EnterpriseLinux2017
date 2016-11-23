# Cheat sheets and checklists Server Setup00

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

	- hosts: pr011
  	become: true
  	roles:
	    - bertvv.rh-base
	    - bertvv.samba
	    - bertvv.vsftpd


> make sure there are 2 spaces infront of any added role

This role has to be downloaded first under the folder /C/Users/Siebert/Documents/elnx-sme-SiebertT/ansible/roles.

## Downloading a role
For Windows, download the scripts from a GitHub Repository as a .zip and place them in the roles folder in the ansible folder.

now run the `roles-deps.sh` script from the directory of the project (Users/Documents/elnx-sme-SiebertT)

You can find the Samba role [here](https://github.com/bertvv/ansible-role-samba)

And the vsftpd role [here](https://github.com/bertvv/ansible-role-vsftpd)

## Add pr011 to the vagrant hosts file

Navigate to the `vagrant-hosts.yml`

Add:

	- name: pr011
	  ip: 172.16.0.11

## Add pr011 to the hosts_vars file

Navigate to the `host_vars` folder in Ansible folder.

Create a new .yml file named pr011.yml. Our configuration will go here.

## Configure pr011.yml

