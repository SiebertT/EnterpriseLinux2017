# Error troubleshooting guide: VBoxSF not available when using latest version of VirtualBox (at moment of writing 5.1.14)

This guide is meant to help users that are struggling to `vagrant up` because of a mismatched Guest Addition versions.

The error may look like the following:

![](https://i.gyazo.com/15a173666f448ce166661855bd36085f.png)

The cause is an old version of the base box. To update this, destroy all guest VMs and remove the base box. A vagrant up after this will download the latest version and should resolve the issue.


## Step by step fix

1. Run `vagrant destroy -f`
2. List your base boxes by typing `vagrant box list`, identify the base box of your guest hosts
3. run `vagrant box remove NAME` to remove the old base box
4. Check your `vagrant box list` once more to check if the box was removed
5. Run the `vagrant up` command. The first thing that will happen is the download of the new version of the removed base box. After this the guest hosts will be upped.
