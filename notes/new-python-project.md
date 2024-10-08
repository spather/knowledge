# Starting a new Python project
Create a new venv and activate it:

```bash
cd path/to/project/root
python3 -m venv .venv --prompt "project_name/.venv" # replace project_name
source .venv/bin/activate
```

Install pip-tools (you have to do this manually vs via a requirements file because you use pip-tools to compile the requirements file). 

```bash
python3 -m pip install pip-tools
```

In project root, create `requirements.in` with the following header:

```python
# Compile this file with:
#   pip-compile --output-file=requirements.txt requirements.in --resolver=backtracking
#
# Then install with
#   pip install -r requirements.txt
```

Then list requirements below this line.

Run `pip-compile` as instructed above to generate `requirements.txt` - this file should be committed to source control, alongside `requirements.in`.

Run `pip-install` as instructed above to install the dependencies.

Now create the requirements files for dev dependencies:

In project root, create `requirements.dev.in` with the following header:

```python
# Compile this file with:
#   pip-compile --output-file=requirements.dev.txt requirements.dev.in --resolver=backtracking
#
# Then install with
#   pip install -r requirements.dev.txt

# Constrain to packages already in main requirements.txt https://github.com/jazzband/pip-tools#workflow-for-layered-requirements
-c requirements.txt
```

Then list requirements below this line. Typical requirements for a new project include:

```
black
ipykernel # If you'll be using Jupyter notebooks
mypy
pip-tools # Even though you manually installed this, put it here so it ends up in the list of dev requirements
```

Run `pip-compile` as instructed above to generate `requirements.dev.txt` - this file should be committed to source control, alongside `requirements.dev.in`.

Run `pip-install` as instructed above to install the dev dependencies.

In the project root, create a `src/` dir and put the project source code there.

Open VS Code on the project root. If it was previously open before you created the venv, you might need to restart it for it to see the python interpreter from the venv. Before you do any coding, make sure you select the Python interpreter from your venv in the bottom status bar. 

Configure the VS Code filewatcher to ignore the venv directory in the project root by creating a `.vscode/settings.json` (in the project root), with the following content:

```
{
  "files.exclude": {
    ".venv": true
  }
}
```

# If you plan to use Jupyter notebooks
Create a `notebooks/` folder as a peer of `src/` (not under `src/`) and put notebooks there. Consider putting the `notebooks/` folder in `.gitignore`. 

Install an iPython kernel for the venv:

```bash
# From a shell in which the venv for the project is activated:
python3 -m ipykernel install --user --name myproj --display-name "Python (myproj)"
```

You may need to restart VS Code for it to see the new kernel. When opening your `.ipynb` files, make sure to select the kernel you just installed above before executing any cells. 


