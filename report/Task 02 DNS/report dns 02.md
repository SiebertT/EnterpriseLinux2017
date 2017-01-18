# Enterprise Linux Lab Report Task 02 DNS

- Student name: [Siebert Timmermans](https://github.com/SiebertT)
- Github repo: [elnx-sme-SiebertT](https://github.com/HoGentTIN/elnx-sme-SiebertT)

The goal of this task is to learn DNS configuration and the BIND role.

## Test plan DNS task 02

- Execute `vagrant status`
- Execute `vagrant up pu001`
- Execute `vagrant up pu002`
- Log in to the server with `vagrant ssh pu001`
- execute `dig @192.0.2.10 pu001.avalon.lan` or `dig @192.0.2.10 pu002.avalon.lan` on either machine
- Run the test runbats.sh on both machines with sudo rights


## Procedure/Documentation DNS 02
My documentation and code is written down in the [cheat sheet](https://github.com/HoGentTIN/elnx-sme-SiebertT/blob/master/report/Task%2002%20DNS/cheat-sheet%20dns%2002.md)

For this task, the following steps were taken:

1. Download the required role and add it to the roles folder in the Ansible folder
2. Add the role + rhbase to site.yml for pu001 and pu002 (master and slave)
3. Add pu001 and pu002 to the host_vars folder (master and slave)
4. Add pu001 and pu002 to the vagrant_hosts.yml (master and slave)
5. Configure pu001.yml and pu002.yml in host_vars with the correct variables found at [elnx-sme-SiebertT](https://github.com/HoGentTIN/elnx-sme-SiebertT)
5. Run the test after you're connected to the server

> In order to get the names and syntax of the variables right, check the role documentation carefully


## Test report Server Setup 00
Every lab report should contain a test plan. To give an idea of what is meant by this, a test plan for this assignment is given here.

- On the host system, go to the local working directory of the project repository
- Execute `vagrant status`
    - At this point pu004 should exist and pu001 + pu002 should be either created or running
- To test the BIND role, we use the testing script. This is sensitive to syntax and the variables. Please make sure your file structure is set up correctly and the new pu001 and pu002 is initialized in the correct places.
- type `dig @192.0.2.10 pu001.avalon.lan` or `dig @192.0.2.10 pu002.avalon.lan`  on master and slave to test the DNS lookup
- change directory to /vagrant inside pu001 and pu002 by typing `cd /vagrant/`
- type `sudo ./test/runbats.sh`
- Receive the following output:

> Mind the _sudo_ addition in order to make the test work.

### Expected output for pu001

```
[vagrant@pu001 ~]$ sudo /vagrant/test/runbats.sh
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
Running test /vagrant/test/pu001/masterdns.bats
✓ The `dig` command should be installed
✓ The main config file should be syntactically correct
✓ The forward zone file should be syntactically correct
✓ The reverse zone files should be syntactically correct
✓ The service should be running
✓ Forward lookups public servers
✓ Forward lookups private servers
✓ Reverse lookups public servers
✓ Reverse lookups private servers
✓ Alias lookups public servers
✓ Alias lookups private servers
✓ NS record lookup
✓ Mail server lookup

13 tests, 0 failures

```

```
[vagrant@pu001 ~]$ dig @192.0.2.10 pu002.avalon.lan

; <<>> DiG 9.9.4-RedHat-9.9.4-29.el7_2.4 <<>> @192.0.2.10 pu002.avalon.lan
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1313
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;pu002.avalon.lan.              IN      A

;; ANSWER SECTION:
pu002.avalon.lan.       604800  IN      A       192.0.2.11

;; AUTHORITY SECTION:
avalon.lan.             604800  IN      NS      pu001.avalon.lan.
avalon.lan.             604800  IN      NS      pu002.avalon.lan.

;; ADDITIONAL SECTION:
pu001.avalon.lan.       604800  IN      A       192.0.2.10

;; Query time: 0 msec
;; SERVER: 192.0.2.10#53(192.0.2.10)
;; WHEN: Wed Jan 18 14:46:37 UTC 2017
;; MSG SIZE  rcvd: 111

[vagrant@pu001 ~]$ dig @192.0.2.10 pu001.avalon.lan

; <<>> DiG 9.9.4-RedHat-9.9.4-29.el7_2.4 <<>> @192.0.2.10 pu001.avalon.lan
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 62067
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;pu001.avalon.lan.              IN      A

;; ANSWER SECTION:
pu001.avalon.lan.       604800  IN      A       192.0.2.10

;; AUTHORITY SECTION:
avalon.lan.             604800  IN      NS      pu002.avalon.lan.
avalon.lan.             604800  IN      NS      pu001.avalon.lan.

;; ADDITIONAL SECTION:
pu002.avalon.lan.       604800  IN      A       192.0.2.11

;; Query time: 0 msec
;; SERVER: 192.0.2.10#53(192.0.2.10)
;; WHEN: Wed Jan 18 14:47:23 UTC 2017
;; MSG SIZE  rcvd: 111

```

### Expected output for pu002

```
$ vagrant ssh pu002
Last login: Tue Dec  6 20:14:56 2016 from 10.0.2.2
Welcome to pu002..
enp0s3     : 10.0.2.15         fe80::a00:27ff:febb:91b7/64
enp0s8     : 192.0.2.11        fe80::a00:27ff:fe9a:8fe6/64
[vagrant@pu002 ~]$ sudo /vagrant/test/runbats.sh
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
Running test /vagrant/test/pu002/slavedns.bats
 ✓ The `dig` command should be installed
 ✓ The main config file should be syntactically correct
 ✓ The server should be set up as a slave
 ✓ The server should forward requests to the master server
 ✓ There should not be a forward zone file
 ✓ The service should be running
 ✓ Forward lookups public servers
 ✓ Forward lookups private servers
 ✓ Reverse lookups public servers
 ✓ Reverse lookups private servers
 ✓ Alias lookups public servers
 ✓ Alias lookups private servers
 ✓ NS record lookup
 ✓ Mail server lookup

14 tests, 0 failures

```

```
[vagrant@pu002 ~]$ dig @192.0.2.10 pu001.avalon.lan

; <<>> DiG 9.9.4-RedHat-9.9.4-29.el7_2.4 <<>> @192.0.2.10 pu001.avalon.lan
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 27972
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;pu001.avalon.lan.              IN      A

;; ANSWER SECTION:
pu001.avalon.lan.       604800  IN      A       192.0.2.10

;; AUTHORITY SECTION:
avalon.lan.             604800  IN      NS      pu001.avalon.lan.
avalon.lan.             604800  IN      NS      pu002.avalon.lan.

;; ADDITIONAL SECTION:
pu002.avalon.lan.       604800  IN      A       192.0.2.11

;; Query time: 0 msec
;; SERVER: 192.0.2.10#53(192.0.2.10)
;; WHEN: Wed Jan 18 14:58:23 UTC 2017
;; MSG SIZE  rcvd: 111
```

```
[vagrant@pu002 ~]$ dig @192.0.2.10 pu002.avalon.lan

; <<>> DiG 9.9.4-RedHat-9.9.4-29.el7_2.4 <<>> @192.0.2.10 pu002.avalon.lan
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 56053
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 2, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;pu002.avalon.lan.              IN      A

;; ANSWER SECTION:
pu002.avalon.lan.       604800  IN      A       192.0.2.11

;; AUTHORITY SECTION:
avalon.lan.             604800  IN      NS      pu001.avalon.lan.
avalon.lan.             604800  IN      NS      pu002.avalon.lan.

;; ADDITIONAL SECTION:
pu001.avalon.lan.       604800  IN      A       192.0.2.10

;; Query time: 0 msec
;; SERVER: 192.0.2.10#53(192.0.2.10)
;; WHEN: Wed Jan 18 15:00:19 UTC 2017
;; MSG SIZE  rcvd: 111

```



## Resources

For config variables + task: [GitHub repo](https://github.com/HoGentTIN/elnx-sme-SiebertT)

For roles: [BIND](https://github.com/bertvv/ansible-role-bind) and [rhbase](https://github.com/bertvv/ansible-role-rh-base)

> Test files in these roles were of great value as well.

[Syllabus](https://chamilo.hogent.be/Chamilo/Libraries/Resources/Javascript/Plugin/PDFJS/web/viewer.html?file=https%3A%2F%2Fchamilo.hogent.be%2Findex.php%3Fapplication%3DChamilo%255CCore%255CRepository%26go%3DDocumentDownloader%26object%3D2327320%26security_code%3D24ed8910204771c621e8dae1f7cbad0e2148b8bc%26display%3D1)
