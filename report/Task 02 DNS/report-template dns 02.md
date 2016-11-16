# Enterprise Linux Lab Report Task 02 DNS

- Student name: 
- Github repo: <https://github.com/HoGentTIN/elnx-USER.git>

Describe the goals of the current iteration/assignment in a short sentence.

## Test plan DNS task 02

- Execute `vagrant status`
- Execute `vagrant up pu004`
- Log in to the server with `vagrant ssh pu004`
- Run the test runbats.sh
- Log off and try to connect as a user through SSH


## Test plan

How are you going to verify that the requirements are met? The test plan is a detailed checklist of actions to take, including the expected result for each action, in order to prove your system meets the requirements. Part of this is running the automated tests, but it is not always possible to validate *all* requirements throught these tests.

## Procedure/Documentation Server Setup 00
My documentation and code is written down in the [cheat sheet](https://github.com/HoGentTIN/elnx-sme-SiebertT/blob/master/report/cheat-sheet.md)

For this task, the following steps were taken:

1. Download the required role and add it to the roles folder in the Ansible folder
2. Add the role to site.yml
3. Add the required packages for every server to all.yml
4. Add and configure the role variables to all.yml
5. Run the test after you're connected to the server

> In order to get the names and syntax of the variables right, check the role documentation carefully

## Procedure/Documentation

Describe *in detail* how you completed the assignment, with main focus on the "manual" work. It is of course not necessary to copy/paste your code in this document, but you can refer to it with a hyperlink.

Make sure to write clean Markdown code, so your report looks good and is clearly structured on Github.

## Test report Server Setup 00
Every lab report should contain a test plan. To give an idea of what is meant by this, a test plan for this assignment is given here.

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

## Test report

The test report is a transcript of the execution of the test plan, with the actual results. Significant problems you encountered should also be mentioned here, as well as any solutions you found. The test report should clearly prove that you have met the requirements.

## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.
