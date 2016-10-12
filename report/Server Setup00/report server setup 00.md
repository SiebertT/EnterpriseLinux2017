# Enterprise Linux Lab Report

- Student name: 
- Github repo: <https://github.com/HoGentTIN/elnx-USER.git>

The goal is to set the server up and take care of the Ansible role + variables for the servers to come.

## Test plan Server Setup 00

- Execute `vagrant status`
- Execute `vagrant up pu004`
- Log in to the server with `vagrant ssh pu004`
- Run the test runbats.sh
- Log off and try to connect as a user through SSH


## Procedure/Documentation Server Setup 00
My documentation and code is written down in the [cheat sheet](https://github.com/HoGentTIN/elnx-sme-SiebertT/blob/master/report/cheat-sheet.md) for this task.

For this task, the following steps were taken:

1. Download the required role and add it to the roles folder in the Ansible folder
2. Add the role to site.yml
3. Add the required packages for every server to all.yml
4. Add and configure the role variables to all.yml
5. Run the test after you're connected to the server

> In order to get the names and syntax of the variables right, check the role documentation carefully


## Test report Server Setup 00

- On the host system, go to the local working directory of the project repository
- Execute `vagrant status`
    - There should be one VM, `pu004` with status `not created`. If the VM does exist, destroy it first with `vagrant destroy -f pu004`
- Execute `vagrant up pu004`
    - The command should run without errors (exit status 0)
- Log in on the server with `vagrant ssh pu004` and run the acceptance tests. They should succeed

    ```
    [vagrant@pu004 test]$ sudo /vagrant/test/runbats.sh
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
    ✓ Custom /etc/motd should be installed

    10 tests, 0 failures
    ```

    Any tests for the LAMP stack may fail, but this is not part of the current assignment.

5. Log off from the server and ssh to the VM as described below. You should **not** get a password prompt.

    ```
    $ ssh siebert@192.0.2.50
    Welcome to pu004.localdomain.
    enp0s3     : 10.0.2.15         fe80::a00:27ff:fe5c:6428/64
    enp0s8     : 192.0.2.50        fe80::a00:27ff:fecd:aeed/64
    [siebert@pu004 ~]$
    ```

## Resources

https://github.com/bertvv/ansible-role-rh-base/tree/master

https://github.com/bertvv/ansible-role-rh-base/tree/tests

Syllabus Linux Enterprise
