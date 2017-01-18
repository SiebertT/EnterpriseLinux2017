# Enterprise Linux Lab Report

- Student name: [Siebert Timmermans](https://github.com/SiebertT)
- Github repo: <https://github.com/HoGentTIN/elnx-sme-SiebertT>

The goal is to setup a Linux Lamp stack through Vagrant and Ansible. To achieve this a host .yml has to be configured with all the correct parts required. The certificates have to be configured as well for security.

## Test plan Lamp Stack 01

- Execute `vagrant status`
- Execute `vagrant up pu004`
- Log in to the server with `vagrant ssh pu004`
	- Errors for this part should be troublshooted in the cheat sheet for Server Setup 00
- Run the test runbats.sh by typing /vagrant/test.runbats.sh
	- All tests should be marked with a V, if this is not the case, troubleshoot using the cheat sheets
- Log in to the mariaDB with your custom user and password
	- If this is still the default, look up the cheat sheet
- Visit the wordpress setup site at 192.0.2.50/wordpress/
	- You should see the setup page, if not, troubleshoot the config for Wordpress in the .yml file

## Procedure/Documentation LAMP stack 01
My documentation and code is written down in the [cheat sheet](https://github.com/HoGentTIN/elnx-sme-SiebertT/blob/master/report/Task%2001%20Lamp/cheat-sheet%20Lamp01.md)

For this task, the following steps were taken:

1. Download the required roles and add it to the roles folder in the Ansible folder
	- the roles for this case were: httpd, mariadb, rh-base and wordpress by [bertvv](https://galaxy.ansible.com/bertvv/)
2. Add the roles to site.yml
3. Create a folder for the host variables in the Ansible folder.
4. Add a .yml file in this new folder with the name of your host
	- in this case pu004.yml
4. Add and configure the role variables in this new .yml file. Use the test documentation written by the maker of the roles.
5. Generate and configure the certificates in the VM itself
6. Add the certificates path to the correct variables in the .yml file
7. Provision Vagrant
5. Run the test after you're connected to the server
	- Remember to change your own custom names and variables in the test files.
6. Connect to the Wordpress site

> In order to get the names and syntax of the variables right, check the role documentation carefully

## Test report Lamp Stack 01

- On the host system, go to the local working directory of the project repository
- Execute `vagrant status`
    - There should be one VM, `pu004` with status `not created`. If the VM does exist, destroy it first with `vagrant destroy -f pu004`
- Execute `vagrant up pu004`
    - The command should run without errors (exit status 0)
- Log in on the server with `vagrant ssh pu004` and run the acceptance tests. They should succeed



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
	Running test /vagrant/test/pu004/lamp.bats
	 ✓ The necessary packages should be installed
	 ✓ The Apache service should be running
	 ✓ The Apache service should be started at boot
	 ✓ The MariaDB service should be running
	 ✓ The MariaDB service should be started at boot
	 ✓ The SELinux status should be ‘enforcing’
	 ✓ Web traffic should pass through the firewall
	 ✓ Mariadb should have a database for Wordpress
	 ✓ The MariaDB user should have "write access" to the database
	 ✓ The website should be accessible through HTTP
	 ✓ The website should be accessible through HTTPS
	 ✓ The certificate should not be the default one
	 ✓ The Wordpress install page should be visible under http://192.0.2.50/wordpress/
	 ✓ MariaDB should not have a test database
	 ✓ MariaDB should not have anonymous users

15 tests, 0 failures


On your host system, visit the WordPress setup page at 192.0.2.50/wordpress/

> should this fail, do visit the cheat sheet for documentation and check if the IP address matches your setup

![](https://i.gyazo.com/ddd0798269b41f771c2f529e67893f1d.png)



## Resources

[Bert Van Vreckems' Ansible and GitHub repositories for the roles and documentation.](https://galaxy.ansible.com/bertvv/)

[CentOS Wiki for the certificate configuration](https://wiki.centos.org/HowTos/Https).
