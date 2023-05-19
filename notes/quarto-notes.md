# Quarto Notes

[Quarto](https://quarto.org/) is a tool for publishing notebooks into various formats. 

Random things I learned:

* Put the `_quarto.yml` file in the `notebooks/` subdirectory, not the project root, or else lots of things will go wrong. 
* In `_quarto.yml`, set the `output-dir` to something that is .gitignored otherwise every render will result in untracked files. 
* If you run `quarto publish gh-pages` locally, it's going to do some funky stuff with checking out the `gh-pages` branch that won't work well with [`pre-commit`](https://pre-commit.com/). If you have `pre-commit` set up in the repo, run the publish command like this:

```bash
PRE_COMMIT_ALLOW_NO_CONFIG=1 quarto publish gh-pages
```
