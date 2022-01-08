# Python Environments
My thinking on this is heavily influenced by [A Guide to Python Environment, Dependency and Package Management: Conda + Poetry](https://ealizadeh.com/blog/guide-to-python-env-pkg-dependency-using-conda-poetry).

Overall you want a solution for two different things:
* Managing environments: keeping the python interpreter and installed packages isolated for each application
* Managing dependencies: tracking which libraries an application uses and resolving dependencies between them

The linked blog post above recommends using [Conda](https://docs.conda.io/projects/conda/en/latest/index.html) to manage environments so that the environments can include non-python things (e.g. [Jupyter Lab](https://jupyter.org/)) in addition to python things. (My preferred way to get conda is via [Miniforge](https://github.com/conda-forge/miniforge).) Other environment managers like venv are Python-only. It recommends using [Poetry](https://python-poetry.org/) as a package manager for Python packages because it does a better job of dependency management and resolution than Conda does. So if a package is available via Poetry and Conda, prefer to install via Poetry. Use Conda primarily for non-Python packages. 

