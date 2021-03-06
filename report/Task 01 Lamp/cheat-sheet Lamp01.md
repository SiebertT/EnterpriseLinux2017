# Cheat sheets and checklists Lampstack 01

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

## Installing the Apache, MariaDB and WordPress roles

For Windows, download the scripts from a GitHub Repository as a .zip and place them in the roles folder in the ansible folder.

now run the `roles-deps.sh` script from the directory of the project (Users/Documents/elnx-sme-SiebertT)

Now add the roles to the site.yml

![](https://i.gyazo.com/e9777c4b7353c2c540602b105b6c2892.png)

Now proceed and provision your Vagrant in the git bash `vagrant provision`

These roles provide a **MariaDB, Apache and WordPress** installation for the **pu004** VM.

## Adding your variable configuration to the host_vars folder in Ansible
1. Create a folder host_vars in the ansible folder for your configuration specifically for a vm.
2. Make a .yml file in the host_vars folder. Name it the same name as your VM to be configured.

This .yml file, in this case pu004.yml, will contain all the variables the VM needs to differentiate itself. By giving it the name of the VM, it will find the configuration listed.

## Adding the httpd (apache) configuration to pu004.yml

![](https://i.gyazo.com/233602b632fd65833507353b8f62e711.png)

> it's possible that the vm doesn't immediatly accept the changes. In case of this happening, try to code below

    [vagrant@pu004 ~]$ sudo firewall-cmd --reload
    success
    [vagrant@pu004 ~]$ sudo firewall-cmd --list-all // you should see that http and https is added to the services.

Here we allow the **firewall variable** from the **rhbase role** to accept **http and https services**. The scripting is specifically set to **php** as well.

## Adding the MariaDB configuration to pu004.yml

![](https://i.gyazo.com/5e7fbdaa4a21483baddadc4b5866ab0c.png)

Here we add and **name** the **mariaDB** database. We add a **user** as well with an **encrypted password** and the **correct priviliges** (in this case all privileges are granted). The **root password for the database** itself is added too.

> when giving priviliges to a user, specify the db or it wont work

> Reminder: If you choose these values yourself and want to use the tests in this repository, change the values in the test files!

## Adding the WordPress configuration to pu004.yml

![](https://i.gyazo.com/187fb16915563af74500020966268be3.png)

Here we first of all declare the **database** wordpress should use, e.g. our mariaDB we just made. We once again add a **user** and a **encrypted password** to use this service. In the configuration are some optional plugins and themes already specified. You are **not required** to add these for a working configuration.

## Certificates

### Generate private key

    openssl genrsa -out ca.key 2048

> This generates a private key, execute this adn the following commands in the VM (pu004) itself.

### Generate CSR

    openssl req -new -key ca.key -out ca.csr

> This generates a Certificate Signing Request or CSR key.

### Generate Self Signed Key

    openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt

> This generates a Self Signed Key

### Copy the files to the correct locations

    cp ca.crt /etc/pki/tls/certs
    cp ca.key /etc/pki/tls/private/ca.key
    cp ca.csr /etc/pki/tls/private/ca.csr

> With these commands we copy the generated keys to the correct directories.

### Update apache SSL config file

	vi +/SSLCertificateFile /etc/httpd/conf.d/ssl.conf

> This command updates the Apache SSL configuration file

### Change paths for key and crt file

	SSLCertificateFile /etc/pki/tls/certs/ca.crt

	SSLCertificateKeyFile /etc/pki/tls/private/ca.key

> Here we change the paths for the CRT and key files to the matching ones.

### Quit and save + restart apache

	sudo httpd -k restart

### Add the path and declare in .yml

![](https://i.gyazo.com/83978c52f246e3e05f6bfdf7870dbda5.png)

Here we declare the SSL certificate file and keyfile in the variables in pu004.yml.

Look up [this site](https://wiki.centos.org/HowTos/Https) for more information about the certificates.

## Automation of the certificates
1. In your VM, generate the certificate and keys like listed above
2. Create a folder in your Ansible folder, copy the .crt and .key to it
3. in your site.yml, add a pretask variable and add the copy statements to place the files in the new folder into the vm
![](https://i.gyazo.com/ffb3ed6527beccacac5ef4630cca52e5.png)
