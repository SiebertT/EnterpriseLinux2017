# Error troubleshooting guide: VBoxSF not available when using latest version of VirtualBox (at moment of writing 5.1.14)

This guide is meant to help users that are struggling to `vagrant up` because of a mismatched Guest Addition error

The error may look like the following:

![](https://i.gyazo.com/15a173666f448ce166661855bd36085f.png)

It appears this error is caused by an outdated **kernel-devel**. This causes `vboxadd.sh` to fail setting up the **vboxadd service**

It can be fixed by entering your guest virtual machine and updating.

## Step by step fix

1. Run `vagrant up -guestVMName-` on your guest VM, encounter the error above
2. SSH into the guest VM by typing `vagrant ssh -guestVMName-`
3. Type `sudo yum update -y` to update, let it happen
4. Once the updating is done, `exit` the guest VM and type `vagrant halt -guestVMName-`
5. After allowing the guest VM to gracefully shut down, type `vagrant up -guestVMName-`
6. Allow this to do its work. To finish up, type `vagrant provision -guestVMName-`
