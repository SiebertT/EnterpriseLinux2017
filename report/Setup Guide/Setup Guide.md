# Vagrant Setup Guide

## Download Git Bash
By default, Windows doesn't know GIT.

Download Git from https://git-scm.com/downloads and add the Bash client.

## Generating an SSH keypair and adding it to GitHub
In your GIT bash client, enter `ssh-keygen`

Choose a file location, copy the public key that was generated.

Add it to your GitHub SSH settings.

## Create a directory to enter your GitHub repository in

Name it appropriately so you can easily find it.

You will have to change directories to this directory every time in order to vagrant up. Keep this in mind for practical reasons.

## Cloning the repository from GitHub with the SSH Clone
Do the following in the Git Bash Client

  $ cd Documents/Courses/EnterpriseLinux/
  $ git clone --config core.autocrlf=input -> PASTE COPIED SSH CLONE HERE <

You should now get the entire repo downloaded and ready for use.

If your configuration is done, one `vagrant up` command should set up your entire environment!
