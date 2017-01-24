# Enterprise Linux Lab Report Task 04 - DHCP

- Student name: Siebert Timmermans
- Github repo: <https://github.com/HoGentTIN/elnx-sme-SiebertT>

Goal is to learn to configure DHCP with the subnets when being provided with ranges.

## Test plan Task 04 - DHCP

- Execute `vagrant status`
- Execute `vagrant up pr001`
- Log in to the server with `vagrant ssh pr001`
- Execute `sudo yum install nmap`
- Test DHCP by typing `sudo nmap --script broadcast-dhcp-discover -e enp0s8`. You need to get a response and see the configured settings in the dhcp discover frame.


## Procedure/Documentation Task 03 - Fileshare
My documentation and code is written down in the [cheat sheet](https://github.com/HoGentTIN/elnx-sme-SiebertT/blob/master/report/Task%2004%20DHCP/cheat-sheet%20DHCP.md)

For this task, the following steps were taken:

1. Download the required role (**DHCP**) and add it to the roles folder in the Ansible folder
2. Add the roles to **site.yml**
3. Add **pr001** to the **vagrant host** file
4. Add **pr001** to the **host_vars** file
5. Configure DHCP with the following
  6. Calculate the subnets
  7. Add the subnets
  8. Add the global domain name and DNS
  9. Add the global router
  10. Test with nmap


> In order to get the names and syntax of the variables right, check the role documentation carefully


## Test report Task 04 - DHCP

```
[vagrant@pr001 ~]$ sudo nmap --script broadcast-dhcp-discover -e enp0s8

Starting Nmap 6.40 ( http://nmap.org ) at 2017-01-24 02:00 UTC
Pre-scan script results:
| broadcast-dhcp-discover:
|   IP Offered: 172.16.0.3
|   DHCP Message Type: DHCPOFFER
|   Server Identifier: 172.16.0.2
|   IP Address Lease Time: 0 days, 0:05:00
|   Subnet Mask: 255.255.128.0
|   Router: 192.0.2.254
|   Domain Name Server: 192.0.2.10, 192.0.2.11
|_  Domain Name: avalon.lan
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 1.06 seconds
```


## Resources


https://github.com/bertvv/ansible-role-dhcp

https://nmap.org/nsedoc/scripts/dhcp-discover.html

[syllabus](https://chamilo.hogent.be/Chamilo/Libraries/Resources/Javascript/Plugin/PDFJS/web/viewer.html?file=https%3A%2F%2Fchamilo.hogent.be%2Findex.php%3Fapplication%3DChamilo%255CCore%255CRepository%26go%3DDocumentDownloader%26object%3D2327320%26security_code%3D24ed8910204771c621e8dae1f7cbad0e2148b8bc%26display%3D1)
