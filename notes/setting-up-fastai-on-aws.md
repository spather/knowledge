# Setting Up FastAI / Jupyter on AWS EC2 Server

First follow steps to create an instance from AMI on the [FastAI site](https://course.fast.ai/start_aws).

Setting up user:

Basic instructions at: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-users.html

# Create user
ubuntu@ip-172-31-27-8:~$ sudo adduser spather

# Make them a sudoer
ubuntu@ip-172-31-27-8:~$ sudo usermod -aG sudo spather

# Switch to new account
ubuntu@ip-172-31-27-8:~$ sudo su - spather
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

spather@ip-172-31-27-8:~$ whoami
spather
spather@ip-172-31-27-8:~$ mkdir .ssh
spather@ip-172-31-27-8:~$ chmod 700 .ssh
spather@ip-172-31-27-8:~$ touch .ssh/authorized_keys
spather@ip-172-31-27-8:~$ chmod 600 .ssh/authorized_keys
spather@ip-172-31-27-8:~$ vim .ssh/authorized_keys

... Now [add appropriate public key there](copy-ssh-public-key-to-remote-server.md)

Then ssh in as spather

# Set up dotfiles

git clone https://github.com/spather/dotfiles.git

Follow instructions in ~/dotfiles/INSTRUCTIONS

Start tmux after all this

# Install Miniconda

Grab link from https://docs.conda.io/en/latest/miniconda.html#linux-installers

Then `wget the_link_from_above` which should download Miniconda3-latest-Linux-x86_64.sh file.

Then run:
bash Miniconda3-latest-Linux-x86_64.sh

and follow the prompts


# Configure jupyter:

If you want to allow external access to jupyter, do the following, but you don't need to do this if you use an ssh tunnel:

$ jupyter notebook --generate-config


Then edit ~/.jupyter/jupyter_notebook_config.py and set:

c.NotebookApp.allow_origin = '*'
c.NotebookApp.allow_remote_access = True
c.NotebookApp.ip = '0.0.0.0'

Instead of the above, prefer using an ssh tunnel and accessing the notebook via localhost:

ssh -N -f -L 8888:localhost:8888 spather@ec2-34-221-203-175.us-west-2.compute.amazonaws.com


# Getting fastbook working

Cloned https://github.com/fastai/fastbook

and started jupyter from inside that directory.

Had all kinds of problems because the python in the Jupyter notebook is not using the one from the conda environment but instead the system one. Also, running !pip runs the old v2.7 version from /usr/local/bin, but just "pip" directly (with no bang) runs a later one but again, it's not from the conda env (I think). Notes below from the investigation but here's what to do to fix it:

1. Kill the jupyter notebook process if running.
2. In the shell in which you will run the jupyter notebook process:

```
conda deactivate
conda activate base
```
This seems to fix up the paths so now python and pip work from the conda environment.

3. Create a kernel that uses the conda environment:

```
(base) [spather@ip-172-31-27-8 ~]
$ python -m ipykernel install --user --name base --display-name "Python (base)"
Installed kernelspec base in /home/spather/.local/share/jupyter/kernels/base
```

4. Restart the jupyter notebook process and when it starts, change to the "Python (base) kernel.

5. Install fastbook from conda:

```
conda install -c fastai fastbook
```

Without this, the "!pip install fastbook" seems to generate errrors.

Some notes on the investigation I did:
- During the investigation of why pip would always resolve to the /usr/local/bin one, I stumbled across some guidance that says to always run python -m pip instead of pip directly (found this in a stack overflow answer but can't find it now). Though it's fixed, now this is probably good to keep in mind.
- Info on using Jupyter with conda environments: https://ipython.readthedocs.io/en/stable/install/kernel_install.html#kernels-for-different-environments. This pointed me to the instruction to install an ipython kernel from within the environment.
- Verified I'm using the right python via:
```
import sys
sys.executable
```

Also `!which python` and `!which pip` work.

- Before I got !pip to point to the right version, I changed the cell that had previously read:
```
!pip install -Uqq fastbook
```

to

```
import sys
!{sys.executable} -m pip install -Uqq fastbook
```

following the guidance I mentioned above. This worked. (recommended at https://jakevdp.github.io/blog/2017/12/05/installing-python-packages-from-jupyter/#How-to-use-Pip-from-the-Jupyter-Notebook)

# Shutting Down at the End of a Session

^C in the shell running jupyter to shut down the notebook
Go to the instances dashboard and stop the instance

# Starting a New Session

Go to instances dashboard: https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#Instances.
Select the instance and start it.
Refresh a few times until the instance state switches to Running.

Copy the public DNS address of the instance from the instances dashboard

Create the localhost SSH tunnel (*on your local laptop*):
```
ssh -N -f -L 8888:localhost:8888 spather@instance-public-dns-address
```

The ssh in and start jupyter

```
ssh spather@instance-public-dns-address

# On the remote server
cd code/fastbook
conda deactivate
conda activate base
jupyter notebook
```