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

## Add SSH to GitHub login

1. `Siebert@Laptop-Siebert MINGW64 ~/Documents
$ ssh-keygen.exe`
2. Go to path where key was generated and copy it
3. On GitHub, login and at profile add the SSH key

## To prevent DOS error
Because SourceTree has the DOS files errors occured.
`Siebert@Laptop-Siebert MINGW64 ~/Documents
$ git clone --config core.autocrlf=input git@github.com:HoGentTIN/elnx-sme-SiebertT.git`


## Add a role to Ansible for the Vagrant VMs
open site.yml in notepad ++

unders roles:, add -bertvv.rh-base

> make sure there are 2 spaces infront of any added role

This role has to be downloaded first under the folder /C/Users/Siebert/Documents/elnx-sme-SiebertT/ansible/roles.

## Downloading a role
1. If you're on Windows, Ansible is not supported. Through a PowerShell script and a GitHub repository you can still get the role downloaded though. 
2. Otherwise `ansible-galaxy install author.namerole`

> Protip: if you're doing Ansible, go Linux


[Role download](https://github.com/bertvv/ansible-role-rh-base/releases)

## Installing packages on all servers made
Open all.yml to make configurations to all servers.

Add the roles to rhbase_install_packages

![](https://i.gyazo.com/76851497fbb7ae9ad816283adb9d0992.png)

## Create a user with the rhbase and make it an admin + add SSH

Open all.yml, this configuration will count for all machines made.

Make sure the admin password is set to an SSH key that you generated in git bash with keygen. You can find the ssh in your user folder at .ssh.

You shouldn't have the the password out there in plain text. Use a [MD5Generator](http://www.miraclesalad.com/webtools/md5.php) to hide the actual password.

![](https://i.gyazo.com/1e1d955c6b5c36fe08380e1dea0b821b.png)

> The group 'wheel' makes the user automatically sudo in CentOS!

> Be very aware of the case sensitive nature of the syntax!

## Enable the custom MOTD that pops after login
	rhbase_motd: true


