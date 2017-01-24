# Cheat sheets and checklists File Share task 03

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
In this Ansible configuration file we will be configuring the SAMBA en vsftpd roles according to our needs.

### Allow the services through the firewall

```
rhbase_firewall_allow_services:
  - samba
  - ftp
```

### Add SAMBA prerequisites

```
samba_netbios_name: FILES // the netbios name of this server

samba_workgroup: AVALON // your server workgroup name goes here

samba_load_printers: // printers attached to the host will not be shared (default)
  - false

samba_shares_root: /srv/shares // the directory where the Samba shares are created
```

### Add the user groups for the users

```
rhbase_user_groups:
  - management
  - technical
  - sales
  - it
```

### Add the SAMBA shares required, fill them in with the users that are allowed to read and or write. Don't forget to add the groups.

```
samba_shares:
  - name: public
    comment: 'Public share, writeable by all members of group ‘users’'
    public: yes
    write_list: +users
    valid_users: +users
    setype: public_content_t
  - name: management
    comment: 'Completely inaccessible for employees outside of this unit'
    write_list: +management
    valid_users: +management
    directory_mode: '0770'
    group: management
  - name: technical
    comment: 'Visible for employees outside their unit, but not writeable'
    write_list: +technical
    valid_users: +management,+sales,+it,+technical
    group: technical
  - name: sales
    comment: 'Visible to management, not writeable'
    write_list: +sales
    group: sales
    valid_users: +sales,+management
  - name: it
    comment: 'Visible to management, not writeable'
    write_list: +it
    valid_users: +it,+management
    group: it
```

### Allow user home directories to be accessible

	samba_load_home: true

### Add the users who will be using the samba shares (mind to hash the passwords)

```
rhbase_users:
  - name: stevenh
    comment: 'Steven Hermans'
    groups:
      - management
      - users
    password: '$6$m2yfsLpp9lZJVMcO$zJGaa4iNz2IKLf5tHbG7/h8LUwiX0DJWtlpTcTBT3nUPU0.JhaPMxBA8ZhxAJzI4JOZryLe1DLQyPs36YOD6V0'
  - name: stevenv
    comment: 'Steven Van Daele'
    groups:
      - technical
      - users
    password: '$6$7Zcz9qqO/Yw9ZDs6$N01TJlSUxT/u.AqHqB6MejQPHa.PcXqIBsRyWhVtUTN8o4Dk7kbpOHTIz2d4tLPRGGgu0RMTq3acg73Y5yy5q.'
  - name: leend
    comment: 'Leen De Meester'
    groups:
      - technical
      - users
    password: '$6$uqUFseJnaFihn92X$Ra5VOhTES0UILaXjGj3w447aCEqSWbNBe/HcQnytcpvRr0RpcvsAg6v1Xq5ta/BLu8aFQycJNcRhiUofusrIe1'
  - name: svena
    comment: 'Sven Aerens'
    groups:
      - sales
      - users
    password: '$6$xOoZ1CAHuDVfKARl$ADQrFr2sMA563f6aJ3ZtyVVR3NIs5JIOKEbj.AvkmtWKP4ccmqFZHe6P9MkwQaHI1Fpmu5vdzg2OGnBgmVw4w/'
  - name: nehirb
    comment: 'Nehir Baayar'
    groups:
      - it
      - users
    password: '$6$jh6CuYN9iZ2WSruM$VImiZwTzLFfOKqLwPrzeBZOozab4sHCz933PzbUIvvRsKd5RIZGjHzilJxmlyMMGyD/GcWn/IbtZ51JIdPLVC0'
    shell: /bin/bash
  - name: alexanderd
    comment: 'Alexander De Coninck'
    groups:
      - technical
      - users
    password: '$6$/Wr/XSohzec25lSU$nqf2nYIasweqagR9mY/yxTfbhs.G.AGXjxvhTgUEKG6B0IEQoHs5qkjeCbo31HZd4BT/HsYh45HHYHQrkyu340'
  - name: krisv
    comment: 'Kris Vanhaverbeke'
    groups:
      - management
      - users
    password: '$6$aQDXX7fqVRmQiVVa$HWBQ18Sy9LDkaBK4cU.2dK47XonS.VHWHVeASpsvZUqXymmZw7JoxtUDO0Cd8eryJ9d8uQwsmly3Rlpkz5RT7/'
  - name: benoitp
    comment: 'Benoit Pluquet'
    groups:
      - sales
      - users
    password: '$6$DF9ntBLdJk3BV6Ro$L9dGnCFHwJnF9cWC3wK9dA5OgXoH1Z/oGK5FG7QrfjO5qOvNkCfYyt1xC75OoaimOAZ5/vn14vOsJSJRLCHor/'
  - name: anc
    comment: 'An Coppens'
    groups:
      - technical
      - users
    password: '$6$unxQEYsndhmAyrQn$V.ch.joDaT1FrHoAZOrsM//BMaVYBUU9cZ/379z8fgR1fcETmESY4OS5mKgE4rPzXd5oSjoFrPF.tW2rt2wBF.'
  - name: elenaa
    comment: 'Elena Andreev'
    groups:
      - management
      - users
    password: '$6$QM1z7Nu1lX8wNue/$.0gX8dfR/IDpAGTFH1/7i9eSX2WsxpRVG5hOF0sibYAB4/.kSU7M0BaID76bx8v6si2ZDTvqPEouQnho3c6bT.'
  - name: evyt
    comment: 'Evy Tilleman'
    groups:
      - technical
      - users
    password: '$6$LNEZwa9OA5/zw.yN$yhLhVcUp7HZxivrb511rVas.1DoJLDTskBWsS.R3QAg6S8UzIDDLlqwmij2UGz0ykpGXNT6pfsaSH8ixqzCpC0'
  - name: christophev
    comment: 'Christophe Van der Meersche'
    groups:
      - it
      - users
    password: '$6$s1qif/EC6ISit7My$5OL/0flKUCU/bEevASZGilMb1GF13eF4aKE/iIbUlqHbCFhzGB2T8WL9WNrnjhB7rBhY5xJkxnfUF6aJMZ6/q1'
    shell: /bin/bash
  - name: stefaanv
    comment: 'Stefaan Vernimmen'
    groups:
      - technical
      - users
    password: '$6$hZcgFNmatH774JTM$ETp/gb5eOAwHzg9xNQPC2V.kz6Loz0Eiv/rc3kntXRY1hiX.HcB73ShWj0J4kvBh8lvT/QwLN.mrVvrmQ2S5d0'
  - name: siebert
    comment: 'admin'
    groups:
      - it
      - users
      - wheel
    password: '$6$mPMJMP2l9tQNa8Ww$9nQ0W7UyTpIccOyMI1SdlLEWqMZlXOTxI1IBCadY0SkcsHSdrCzetMxGWpzN.OFNROHX4sYwcxdtR8WlY38Q/.'
    shell: /bin/bash
```

### Add the same users as SAMBA users

```
samba_users:
  - name: stevenh
    password: stevenh
  - name: stevenv
    password: stevenv
  - name: leend
    password: leend
  - name: svena
    password: svena
  - name: nehirb
    password: nehirb
  - name: alexanderd
    password: alexanderd
  - name: krisv
    password: krisv
  - name: benoitp
    password: benoitp
  - name: anc
    password: anc
  - name: elenaa
    password: elenaa
  - name: evyt
    password: evyt
  - name: christophev
    password: christophev
  - name: stefaanv
    password: stefaanv
  - name: siebert
    password: letmein123
```

### Finish with the vfstp configuration

```
vsftpd_anonymous_enable: false  // anonymous logins are not permitted
vsftpd_connect_from_port_20: true // Port style data uses port 20 on the server machine
vsftpd_local_enable: true // logins from registered users are allowed
vsftpd_listen: true // runs vsftpd in standalone, ipv6 listen needs to be false because this is true
vsftpd_listen_ipv6: false
vsftpd_write_enable: true // FTP commands are allowed to change the filesystem
vsftpd_local_root: /srv/shares // this is the directory for registered users, defaults to their home directory
```
