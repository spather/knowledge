# Synology NAS setup

## Enable things in control panel 

User & Group, Advanced, check "Enable user home service"

Terminal & SNMP, check "Enable SSH service"

File Services, rsync, check "Enable rsync service"

## Enable ssh without a password

### Steps on the Synology NAS
First, connect via SSH with a password:

```bash
ssh <Diskstation IP> # Then enter password
```

Edit /etc/ssh/sshd_config to enable auth via public key:
* `sudo vim /etc/ssh/sshd_config`
* Uncomment these lines:

```
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

Set up the .ssh directory:
```
spather@DiskStation:~$ cd
spather@DiskStation:~$ mkdir .ssh
spather@DiskStation:~$ touch .ssh/authorized_keys
spather@DiskStation:~$ chmod 700 .ssh
spather@DiskStation:~$ chmod 644 .ssh/authorized_keys
```

Set the right permissions on the home dir (by default they are too permissive and ssh won't let you use public key auth with it set up this way):

```
spather@DiskStation:~$ cd /var/services/homes
spather@DiskStation:/var/services/homes$ chmod 755 spather
```

### Steps on the machine you want to connect from

* If you don't already have a key pair, generate one with `ssh-keygen`. Leave passphrase blank.
* Copy contents of .ssh/id_rsa.pub to the remote server's .ssh/authorized_keys:

`ssh-copy-id -i ~/.ssh/id_rsa <Diskstation IP>` 

e.g. `ssh-copy-id -i ~/.ssh/id_rsa 10.1.10.236`

Replace id_rsa if you used a different file name for the key.

Now you should be able to ssh without a password.

References: 
* https://gnax.io/backup-to-your-synology-nas-with-rsync/
* https://logs.paulooi.com/how-to-rsync-from-remote-server-to-synology.php
* https://superuser.com/questions/736055/ssh-with-no-password-passwordless-on-synology-dsm-5-as-other-non-root-user
* https://www.chainsawonatireswing.com/2012/01/15/ssh-into-your-synology-diskstation-with-ssh-keys/

## Install [SynoCli Network Tools](https://synocommunity.com/package/synocli-net)

First, enable [SynoCommunity](https://synocommunity.com/) as a Package Source:

Open Package Center, Settings, Package Sources tab, click Add, then enter:
- Name: SynoCommunity 
- Location: https://packages.synocommunity.com/

(or do whatever https://synocommunity.com/#easy-install says)

Then use Package Center to install [SynoCli Network Tools](https://synocommunity.com/package/synocli-net)



