# Importing Python Modules from the Local Source Tree in Jupyter Notebooks

The problem: You're working on a Python project and want to use a Jupyter Notebook for some interactive experiments with the code. You'd like to be able to import and use the Python modules from the project (regular Python code in .py files) in the Jupyter notebook. You also want to be able to make changes to the project code and have these take immediate effect in the Jupyter notebook. 

First, enable module [auto reload](https://ipython.org/ipython-doc/3/config/extensions/autoreload.html) in the Jupyter notebook - put these lines in a cell and run them:

```jupyter
%load_ext autoreload
%autoreload 2
```

Then, add the path to your code to `sys.path` in the Jupyter notebook:
```python
# Set up sys.path to enable importing modules from ../src
import os
from pathlib import Path
import sys

# Get path to this notebook
nbpath = os.path.dirname(os.path.realpath("__file__"))

# Replace this with whatever the right path is to your source code.
# This assumes it lives in a `src` directory that's a sibling of 
# the directory in which the notebook lives. 
srcpath = (Path(nbpath) / "../src").resolve()

if str(srcpath) not in sys.path:
    sys.path.append(str(srcpath))

```

Now you should be able to `import` your code's modules from the Jupyter notebook. 
