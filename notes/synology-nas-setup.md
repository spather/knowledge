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
```bash

At this point, you can start tmux:

```bash
tmux
```

## Set up Shared Folders
Go to Control Panel/Shared Folder and create the following shared folders. For each, uncheck "Enable Recycle Bin", check "Enable data checksum for advanced data integrity", and give the spather account Read/Write access:

* GoProImport
* Misc
* Photos
* Videos

Verify that you can connect to these via Finder on the Mac. 

