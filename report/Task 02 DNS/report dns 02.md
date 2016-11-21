# Enterprise Linux Lab Report Task 02 DNS

- Student name: [Siebert Timmermans](https://github.com/SiebertT) 
- Github repo: [elnx-sme-SiebertT](https://github.com/HoGentTIN/elnx-sme-SiebertT)

The goal of this task is to learn DNS configuration and the BIND role.

## Test plan DNS task 02

- Execute `vagrant status`
- Execute `vagrant up pu001`
- Log in to the server with `vagrant ssh pu001`
- execute `dig @192.0.2.10 pu001.avalon.lan`
- Run the test runbats.sh


## Procedure/Documentation DNS 02
My documentation and code is written down in the [cheat sheet](https://github.com/HoGentTIN/elnx-sme-SiebertT/blob/master/report/Task%2002%20DNS/cheat-sheet%20dns%2002.md)

For this task, the following steps were taken:

1. Download the required role and add it to the roles folder in the Ansible folder
2. Add the role + rhbase to site.yml for pu001
3. Add pu001 to the host_vars folder
4. Add pu001 to the vagrant_hosts.yml
5. Configure pu001.yml in host_vars with the correct variables found at [elnx-sme-SiebertT](https://github.com/HoGentTIN/elnx-sme-SiebertT)
5. Run the test after you're connected to the server

> In order to get the names and syntax of the variables right, check the role documentation carefully

## Procedure/Documentation

Describe *in detail* how you completed the assignment, with main focus on the "manual" work. It is of course not necessary to copy/paste your code in this document, but you can refer to it with a hyperlink.

Make sure to write clean Markdown code, so your report looks good and is clearly structured on Github.

## Test report Server Setup 00
Every lab report should contain a test plan. To give an idea of what is meant by this, a test plan for this assignment is given here.

- On the host system, go to the local working directory of the project repository
- Execute `vagrant status`
    - At this point pu004 should exist and pu001 should be either created or running
- To test the BIND role, we use the testing script. This is sensitive to syntax and the variables. Please make sure your file structure is set up correctly and the new pu001 is initialized in the correct places.
- type `dig @192.0.2.10 pu001.avalon.lan` to test the DNS lookup
- change directory to /vagrant inside pu001 by typing `cd /vagrant/`
- type `./test/runbats.sh`
- Receive the following output:

![](https://i.gyazo.com/97b67aefe7ce52a8705347f316aefb45.png)





## Test report

The test report is a transcript of the execution of the test plan, with the actual results. Significant problems you encountered should also be mentioned here, as well as any solutions you found. The test report should clearly prove that you have met the requirements.

## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.
