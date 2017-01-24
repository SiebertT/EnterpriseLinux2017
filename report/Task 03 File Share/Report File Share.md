# Enterprise Linux Lab Report Task 03 - Fileshare

- Student name: Siebert Timmermans
- Github repo: <https://github.com/HoGentTIN/elnx-sme-SiebertT>

Goal is to use SAMBA and VSFTPD services to create a fileshare server with Ansible and Vagrant

## Test plan Task 03 - Fileshare

### General

- Execute `vagrant status`
- Execute `vagrant up pr011`
- Log in to the server with `vagrant ssh pr011`
- Run the test runbats.sh by typing `sudo /vagrant/tests/runbats.sh`

### SAMBA

- Type `smbclient -L //files/` to get an overview of the shares.
- Login as a user by typing `smbclient //files/public -Usvena%svena` to test the public share that is available to all groups to read and write.
- Check the read and write possibilites in the public share by typing `ls`, `md test` and `ls` again. This should have no restrictions and thus no errors.
- Check the access on the management share by typing `smbclient //files/management -Usvena%svena`. This should give a **NT_STATUS_ACCESS_DENIED** error
- Check if a user from the group sales can reach the sales share by typing `smbclient //files/sales -Usvena%svena`, you should be able to access, read and write on this share.
- Check if a user from management has no write rights on the it share by typing  `smbclient //files/it -Ustevenh%stevenh` you should be able to use `ls` but `md test` should give a **STATUS_MEDIA_WRITE_PROTECTED** error
- Make sure an unregistered user cannot login to the public share by typing `smbclient //files/public -U%`, this should give a **NT_STATUS_ACCESS_DENIED**
- Open your Windows Explorer, type `\\172.16.0.11` in the address bar. You should be prompted to login with a user and you can manipulate the the files your user has the rights for.

### VSFTPD

Use [FileZilla](https://filezilla-project.org/) to test vsftpd.

- Log in as siebert from group it with `siebert@172.16.0.11`, fill in the password, login should be succesful with this user
- Check if you get denied from entering the **management** share. The rest should be accessible.
- Check if you can make a file in share **it**
- Attempt to log in as an anonymous user. This should fail


## Procedure/Documentation Task 03 - Fileshare
My documentation and code is written down in the [cheat sheet](https://github.com/HoGentTIN/elnx-sme-SiebertT/blob/master/report/Task%2003%20File%20Server/cheat-sheet%20File%20Server.md)

For this task, the following steps were taken:

1. Download the required role (**samba** and **vsftpd**) and add it to the roles folder in the Ansible folder
2. Add the roles to **site.yml**
3. Add pr011 to the vagrant host file
4. Add pr011 to the host_vars file
5. Configure SAMBA with the following
  6. Add the user groups for the users
  7. Add the SAMBA shares
  8. Allow user home directories
  9. Add users that will use the samba shares
  10. Add the same Users as SAMBA users
11. Configure VSFTPD
  12. Do not permit anonymous logins
  13. Allow logins from registered users
  14. Run vsftpd in standalone mode
  15. Allow FTP commands to change the filesystem
  16. Add the directory for registered users

> In order to get the names and syntax of the variables right, check the role documentation carefully


## Test report Task 03 - Fileshare

### General

```
$ vagrant ssh pr011
Welcome to pr011..
enp0s3     : 10.0.2.15         fe80::a00:27ff:fe8e:91e0/64
enp0s8     : 172.16.0.11       fe80::a00:27ff:fede:8dbb/64
[vagrant@pr011 ~]$ sudo /vagrant/test/runbats.sh
--2017-01-23 23:05:39--  https://github.com/sstephenson/bats/archive/v0.4.0.tar.gz
Resolving github.com (github.com)... 192.30.253.112, 192.30.253.113
Connecting to github.com (github.com)|192.30.253.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://codeload.github.com/sstephenson/bats/tar.gz/v0.4.0 [following]
--2017-01-23 23:05:39--  https://codeload.github.com/sstephenson/bats/tar.gz/v0.4.0
Resolving codeload.github.com (codeload.github.com)... 192.30.253.120, 192.30.253.121
Connecting to codeload.github.com (codeload.github.com)|192.30.253.120|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 17258 (17K) [application/x-gzip]
Saving to: ‘v0.4.0.tar.gz’

100%[======================================================================================================================================================>] 17,258      --.-K/s   in 0.1s

2017-01-23 23:05:40 (168 KB/s) - ‘v0.4.0.tar.gz’ saved [17258/17258]

Running test /vagrant/test/common.bats
 ✓ EPEL repository should be available
 ✓ Bash-completion should have been installed
 ✓ bind-utils should have been installed
 ✓ Git should have been installed
 ✓ Nano should have been installed
 ✓ Tree should have been installed
 ✓ Vim-enhanced should have been installed
 ✓ Wget should have been installed
 ✓ Admin user siebert should exist
 ✓ Custom /etc/motd should have been installed

10 tests, 0 failures
Running test /vagrant/test/pr011/samba.bats
 ✓ The ’nmblookup’ command should be installed
 ✓ The ’smbclient’ command should be installed
 ✓ The Samba service should be running
 ✓ The Samba service should be enabled at boot
 ✓ The WinBind service should be running
 ✓ The WinBind service should be enabled at boot
 ✓ The SELinux status should be ‘enforcing’
 ✓ Samba traffic should pass through the firewall
 ✓ Check existence of users
 ✓ Checks shell access of users
 ✓ Samba configuration should be syntactically correct
 ✓ NetBIOS name resolution should work
 ✓ read access for share ‘public’
 ✓ write access for share ‘public’
 ✓ read access for share ‘management’
 ✓ write access for share ‘management’
 ✓ read access for share ‘technical’
 ✓ write access for share ‘technical’
 ✓ read access for share ‘sales’
 ✓ write access for share ‘sales’
 ✓ read access for share ‘it’
 ✓ write access for share ‘it’

22 tests, 0 failures
Running test /vagrant/test/pr011/vsftp.bats
 ✓ VSFTPD service should be running
 ✓ VSFTPD service should be enabled at boot
 ✓ The ’curl’ command should be installed
 ✓ The SELinux status should be ‘enforcing’
 ✓ FTP traffic should pass through the firewall
 ✓ VSFTPD configuration should be syntactically correct
 ✓ Anonymous user should not be able to see shares
 ✓ read access for share ‘public’
 ✓ write access for share ‘public’
 ✓ read access for share ‘management’
 ✓ write access for share ‘management’
 ✓ read access for share ‘technical’
 ✓ write access for share ‘technical’
 ✓ write access for share ‘sales’
 ✓ write access for share ‘it’

15 tests, 0 failures

```

### SAMBA

```
[vagrant@pr011 ~]$ smbclient -L //files/
WARNING: The "syslog only" option is deprecated
WARNING: The "syslog" option is deprecated
Enter vagrant's password:
Domain=[AVALON] OS=[Windows 6.1] Server=[Samba 4.4.4]

        Sharename       Type      Comment
        ---------       ----      -------
        public          Disk      Public share, writeable by all members of group ‘users’
        management      Disk      Completely inaccessible for employees outside of this unit
        technical       Disk      Visible for employees outside their unit, but not writeable
        sales           Disk      Visible to management, not writeable
        it              Disk      Visible to management, not writeable
        IPC$            IPC       IPC Service (Fileserver files)
Domain=[AVALON] OS=[Windows 6.1] Server=[Samba 4.4.4]

        Server               Comment
        ---------            -------
        FILES                Fileserver nmbd

        Workgroup            Master
        ---------            -------
        AVALON               FILES

[vagrant@pr011 ~]$ smbclient //files/public -Usvena%svena
WARNING: The "syslog only" option is deprecated
WARNING: The "syslog" option is deprecated
Domain=[AVALON] OS=[Windows 6.1] Server=[Samba 4.4.4]
smb: \> ls
  .                                   D        0  Mon Jan 23 23:06:00 2017
  ..                                  D        0  Mon Jan 23 23:00:23 2017

                43977748 blocks of size 1024. 42530720 blocks available
smb: \> md test
smb: \> ls
  .                                   D        0  Mon Jan 23 23:20:10 2017
  ..                                  D        0  Mon Jan 23 23:00:23 2017
  test                                D        0  Mon Jan 23 23:20:10 2017

                43977748 blocks of size 1024. 42530700 blocks available
smb: \> exit
[vagrant@pr011 ~]$ smbclient //files/management -Usvena%svena
WARNING: The "syslog only" option is deprecated
WARNING: The "syslog" option is deprecated
Domain=[AVALON] OS=[Windows 6.1] Server=[Samba 4.4.4]
tree connect failed: NT_STATUS_ACCESS_DENIED
[vagrant@pr011 ~]$ smbclient //files/sales -Usvena%svena
WARNING: The "syslog only" option is deprecated
WARNING: The "syslog" option is deprecated
Domain=[AVALON] OS=[Windows 6.1] Server=[Samba 4.4.4]
smb: \> ls
  .                                   D        0  Mon Jan 23 23:06:06 2017
  ..                                  D        0  Mon Jan 23 23:00:23 2017

                43977748 blocks of size 1024. 42530660 blocks available
smb: \> md test
smb: \> ls
  .                                   D        0  Mon Jan 23 23:21:45 2017
  ..                                  D        0  Mon Jan 23 23:00:23 2017
  test                                D        0  Mon Jan 23 23:21:45 2017

                43977748 blocks of size 1024. 42530720 blocks available
smb: \> exit
[vagrant@pr011 ~]$ smbclient //files/it -Ustevenh%stevenh
WARNING: The "syslog only" option is deprecated
WARNING: The "syslog" option is deprecated
Domain=[AVALON] OS=[Windows 6.1] Server=[Samba 4.4.4]
smb: \> ls
  .                                   D        0  Mon Jan 23 23:06:07 2017
  ..                                  D        0  Mon Jan 23 23:00:23 2017

                43977748 blocks of size 1024. 42530640 blocks available
smb: \> md test
NT_STATUS_MEDIA_WRITE_PROTECTED making remote directory \test
smb: \> ls
  .                                   D        0  Mon Jan 23 23:06:07 2017
  ..                                  D        0  Mon Jan 23 23:00:23 2017

                43977748 blocks of size 1024. 42530640 blocks available
smb: \>


[vagrant@pr011 ~]$ smbclient //files/public -U%
WARNING: The "syslog only" option is deprecated
WARNING: The "syslog" option is deprecated
Domain=[AVALON] OS=[Windows 6.1] Server=[Samba 4.4.4]
tree connect failed: NT_STATUS_ACCESS_DENIED
[vagrant@pr011 ~]$

```

![](https://i.gyazo.com/061834ede9fc89dc4ed3a5664b3fc077.png)


### VSFTPD

```
Commando:	open "siebert@172.16.0.11" 20
Status:	Verbinden met 172.16.0.11:21...
Status:	Verbinding aangemaakt, welkomstbericht afwachten...
Antwoord:	220 (vsFTPd 3.0.2)
Commando:	USER siebert
Antwoord:	331 Please specify the password.
Commando:	PASS **********
Antwoord:	230 Login successful.
```

![](https://i.gyazo.com/104ed22a7ccdbd4f7b40a79f3e89f2eb.png)

## Resources


https://github.com/bertvv/ansible-role-samba

https://github.com/bertvv/ansible-role-vsftpd

http://www.computerhope.com/unix/smbclien.htm

[syllabus](https://chamilo.hogent.be/Chamilo/Libraries/Resources/Javascript/Plugin/PDFJS/web/viewer.html?file=https%3A%2F%2Fchamilo.hogent.be%2Findex.php%3Fapplication%3DChamilo%255CCore%255CRepository%26go%3DDocumentDownloader%26object%3D2327320%26security_code%3D24ed8910204771c621e8dae1f7cbad0e2148b8bc%26display%3D1)
