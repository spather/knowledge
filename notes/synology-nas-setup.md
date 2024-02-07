# Synology NAS setup

## Enable things in control panel 

User & Group, Advanced, check "Enable user home service"

Terminal & SNMP, check "Enable SSH service"

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

## Install SynoCommunity Packages

First, enable [SynoCommunity](https://synocommunity.com/) as a Package Source:

Open Package Center, Settings, Package Sources tab, click Add, then enter:
- Name: SynoCommunity 
- Location: https://packages.synocommunity.com/

(or do whatever https://synocommunity.com/#easy-install says)

Then use Package Center to install:
* [SynoCli Network Tools](https://synocommunity.com/package/synocli-net)
* [Git](https://synocommunity.com/package/git)

## Install Entware
Condensed instructions from https://www.technorabilia.com/using-entware-on-synology-nas/:

Make the necessary directories:

```bash
sudo mkdir -p /volume1/@Entware/opt /opt
sudo mount -o bind /volume1/@Entware/opt /opt
sudo chmod 0755 /opt
```

Assuming your processor is x64, run the following (for other architectures, see specific paths in [article above](https://www.technorabilia.com/using-entware-on-synology-nas/)):
```
wget -O - http://bin.entware.net/x64-k3.2/installer/generic.sh | sudo /bin/sh
```

Create a triggered user-defined task in Task Scheduler (all instructions from [article above](https://www.technorabilia.com/using-entware-on-synology-nas/):

* DiskStation Manager > Control Panel > Task Scheduler
* Create > Triggered Task > User Defined Script
  * General
    * Task: Entware
    * User: root
    * Event: Boot-up
    * Pretask: none
  * Task Settings
    * Run Command: (see below)

Fill in the following run command:

```bash
#!/bin/sh

# Mount/Start Entware
mkdir -p /opt
mount -o bind "/volume1/@Entware/opt" /opt
/opt/etc/init.d/rc.unslung start

# Add Entware Profile in Global Profile
if grep  -qF  '/opt/etc/profile' /etc/profile; then
	echo "Confirmed: Entware Profile in Global Profile"
else
	echo "Adding: Entware Profile in Global Profile"
cat >> /etc/profile <<"EOF"

# Load Entware Profile
. /opt/etc/profile
EOF
fi

# Update Entware List
/opt/bin/opkg update
```

Reboot the NAS.

I didn't see this documented in the articles, but to do anything useful with opkg, I found I had to do the following:

```bash
sudo chmod 644 /opt/etc/opkg.conf
sudo chmod 644 /opt/lib/opkg/status
```
## Install Required Software

* Mg (emacs clone):
```bash
sudo opkg install mg
```
* Newer versions of bash and tmux:

```bash
sudo opkg install tmux
sudo opkg install bash
```

And make bash the default shell by editing `/etc/passwd` and changing the shell for user spather to whatever `which bash` returns after the preceding commands.

## Clone Github repos

### Set up ssh key to access GitHub
Create a new ssh key **on the Diskstation** i.e. run these commands on a terminal session ssh'd into the Disktation:

```bash
ssh-keygen -t ed25519 -C "shyam.pather@gmail.com"
```

Create an ssh config file if it doesn't already exist

```bash
 [ ! -e ~/.ssh/config ] && touch ~/.ssh/config
```

Edit .ssh config and add the following:

```
Host github.com
  AddKeysToAgent yes
  IdentityFile ~/.ssh/id_Diskstation2_github # or whatever filename you chose
```

Make sure the agent is running:

```bash
eval "$(ssh-agent -s)"
```

Add key to agent:

```bash
ssh-add ~/.ssh/id_Diskstation2_github # or whatever your filename is
```

Add the new key to Github account per https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account. 

### Clone Repos

```bash
mkdir -p ~/code
cd ~/code
git clone git@github.com:spather/dotfiles.git
git clone git@github.com:spather/photos_scripts.git
```

## Set up dotfiles and Terminal Environment

### Configure dotfiles
Assuming you completed the steps above to clone the [dotfiles repo](https://github.com/spather/dotfiles/), do the following to set it up. 

>Note: These instructions are a Diskstation-specific instantiation of the instructions at https://github.com/spather/dotfiles/blob/master/INSTRUCTIONS, but may not be up to date as those evolve. Always check those instructions first for any new or changed steps.

Set up symlinks for bash init files:

```bash
cd
ln -s ~/code/dotfiles/ ~/dotfiles
ln -s ~/dotfiles/.bashrc .bashrc
ln -s ~/dotfiles/.bashrc-linux .bashrc-site-specific
ln -s dotfiles/.bash_profile .bash_profile
```

At this point, you can `source ~/.bashrc` to get the bash environment loaded. 

### Configure git
Configure some git things:
```bash
git config --global core.excludesfile ~/dotfiles/config/xplat/git/gitignore_global
git config --global user.name "Shyam Pather"
git config --global user.email "shyam.pather@gmail.com"
```

### Configure tmux

```bash
ln -s dotfiles/.tmux.conf .tmux.conf
ln -s dotfiles/.tmux.conf-linux .tmux.conf-site-specific
```

At this point, you can start tmux:

```bash
tmux
```

## Set up Shared Folder
Go to Control Panel/Shared Folder and create a shared folder called "NAS_Storage". Uncheck "Enable Recycle Bin", check "Enable data checksum for advanced data integrity", and give the spather account Read/Write access:

In a terminal create the following directories, which will be the destinations for syncing: 

```bash
mkdir -p /volume1/NAS_Storage/synced/GoProImport
mkdir -p /volume1/NAS_Storage/synced/Misc
mkdir -p /volume1/NAS_Storage/synced/Photos
mkdir -p /volume1/NAS_Storage/synced/Videos
```

Verify that you can connect to NAS_Storage via Finder on the Mac. 

## Set up Photo Scripts Syncing

### Make Logs Directory
```bash
sudo mkdir /var/log/photo_scripts
sudo chown:users spather /var/log/photo_scripts/
```

### Restore Initial Data from USB Backup

Run `~/code/photos_scripts/sync/diskstation-setup-sync-all-from-usb1` to restore from attached USB1 drive. This will probably take several days to complete. 

>If you're restoring from USB2 or another source, you'll need to create a similar script for that source.

### Set Up `rsync` Service

>Reference for this whole section: https://en.jveweb.net/archives/2011/01/running-rsync-as-a-daemon.html

In DSM, go to Control Panel/File Services, rsync tab and check "Enable rsync service". Leave other settings at their defaults. 

In the terminal edit or create `/etc/rsyncd.conf` with the following content:

```
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
use chroot = no
reverse lookup = no
refuse options = acls

[synced]
    path = /volume1/NAS_Storage/synced
    comment = Root folder for synced directories
    uid = spather
    gid = users
    read only = no
    list = yes
    auth users = spather
    secrets file = /etc/rsyncd.secrets
```

> You will likely need to elevate permissions to edit this file.

> Make sure uid and gid are set to the user/group name of the user/group you want to have applied to files copied to this server via rsync.

Create/edit the `/etc/rsyncd.secrets` file and enter a line like:

```
spather:SomePassWord
```

Taking care that the account name specified here is the same as the one listed in the `auth users` field of the "synced" module in the `/etc/rsyncd.conf` file above. Replace "SomePassWord" with something created by a strong password generator. Save this in a password manager like 1Password as you'll need it every time you rsync to this server. 

Ensure that the `/etc/rsyncd.secrets` is owned by root:root and has only read/write permissions assigned to the owner (`sudo chmod 600 /etc/rsyncd.secrets` will accomplish this). 

After all this, you need to restart the rsync daemon. I don't know of a better way but this works:

* Go to Control Panel/File Services, rsync tab in DSM.
* Uncheck "Enable rsync service" and save changes.
* Check "Enable rsync service" and save changes.

You can test that it's all working by running this on another machine (replacing `./src_dir` with some directory that has a small number of files you want to test syncing:

```bash
rsync -arvzL ./src_dir/ spather@diskstation2.localdomain::synced/test
```

It should prompt for a password and then create the `test` directory on the destination `/volume1/NAS_Storage/synced` and sync the files over. Remove this test directory when you're done testing. 

> As of this writing, enabling the rsync service in DSM causes the daemon to be run under a user account called "SynoRsyncd". I didn't see any options to change this. I expected to have to run the service as root to have it be able to alter file ownership/permissions, but it seems to work as is, so I'm leaving it alone. 
