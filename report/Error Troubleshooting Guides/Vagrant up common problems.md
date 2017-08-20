# Vagrant up common problems

## Guest Additions mismatch
If you happen to receive an error regarding Guest Additions mismatches when you try to `vagrant up` a Virtual Machine, it is in your best interest to completely remove VirtualBox and all of its compononents from your computer and start fresh.

This includes removing the files that are held on by VirtualBox when an uninstall is performed.

Please check the Vagrant/VirtualBox Setup guide at **TO DO** to start over fresh in the right way.

## MariaDB role error - expected different attribute
If you notice this kind of error, there is a fault in the syntax of your .yml document.

This may be caused by a new version of the role you use.

Because of this, it is wise to check the roles you use for a changelog after not using the role for a longer time.

In this case the issue was easily solved by writing the 'name' attribute slightly differently.

## Bringing up VyOS with ansible attached to it from the playbook script.
OSes such as VyOS are meant to be configured either manually through SSH or through a configuration script. This can't and shouldn't be done through Ansible.

Therefore, it is important that the deployment script skips the attempt to configure the VM through Ansible. It should immediatly be forwarded to the configuration script for VyOS.

This was fixed by updating the playbook script to stop forcing ansible on VyOS.
