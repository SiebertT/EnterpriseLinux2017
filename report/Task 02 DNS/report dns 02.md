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

###For pu001

![](https://i.gyazo.com/97b67aefe7ce52a8705347f316aefb45.png)

![](https://i.gyazo.com/661236deb101232bc32b5aabdfccc072.png)

###For pu002

![](https://i.gyazo.com/95f77d943ad092e0c6b25d454c0226e2.png)

![](https://i.gyazo.com/babcf37d68bdd594e9d696695c604cf6.png)


## Resources

For config variables + task: [GitHub repo](https://github.com/HoGentTIN/elnx-sme-SiebertT)

For roles: [BIND](https://github.com/bertvv/ansible-role-bind) and [rhbase](https://github.com/bertvv/ansible-role-rh-base)

> Test files in these roles were of great value as well.

[Syllabus](https://chamilo.hogent.be/Chamilo/Libraries/Resources/Javascript/Plugin/PDFJS/web/viewer.html?file=https%3A%2F%2Fchamilo.hogent.be%2Findex.php%3Fapplication%3DChamilo%255CCore%255CRepository%26go%3DDocumentDownloader%26object%3D2327320%26security_code%3D24ed8910204771c621e8dae1f7cbad0e2148b8bc%26display%3D1)