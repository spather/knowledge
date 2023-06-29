# Dev Setup for Using Lambda Cloud GPUs

Some notes on setting up a dev environment for use with [Lambda](https://cloud.lambdalabs.com/).

Create a [filesystem](https://lambdalabs.com/blog/persistent-storage-beta) so you can save data between sessions. When creating a new instance, attach the filesystem and all the data will be there. Note, not all instance types suppport attaching filesystems.

## Jupyter Kernel Access Via VSCode
The default Lambda setup wants you to use the JupyterLab web IDE. But you can still use VSCode:

* In the config file in ~/.jupyter/jupyter.config.py, you'll find lines that set `c.ServerApp.local_hostnames` and `c.ServerApp.token`.
* The following bash script will turn these into the appropriate URL:

```sh
#!/usr/bin/env bash
set -euo pipefail

JUPYTER_LAB_CONFIG="${HOME}/.jupyter/jupyter_config.py"

TOKEN_LINE=$(cat "${JUPYTER_LAB_CONFIG}" | grep 'c.ServerApp.token')
HOSTNAMES_LINE=$(cat "${JUPYTER_LAB_CONFIG}" | grep 'c.ServerApp.local_hostnames')

python3 - << EOF
import types
c = types.SimpleNamespace()
c.ServerApp = types.SimpleNamespace()
${TOKEN_LINE}
${HOSTNAMES_LINE}
print(f"https://{c.ServerApp.local_hostnames[0]}?token={c.ServerApp.token}")
EOF
```

## VSCode Remote SSH Setup
Use [these instructions](https://code.visualstudio.com/docs/remote/ssh#_remember-hosts-and-advanced-settings) to set up a config for the remote host to connect with the right key file. 

Connect once (this will set up the vscode server on the remote). Disconnect by closing that vscode window.

SSH into the instance and move the .vscode server directory into the shared filesystem so you do not have to re-create it every time you spin up a new instance:

```sh
mv ~/.vscode-server path/to/filesystem/
ln -s path/to/filesystem/.vscode-server ~/.vscode-server
```



