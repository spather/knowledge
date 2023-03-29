# LaTeX Notes

# Install on Mac
Install MacTex
```
brew install --cask mactex
```

Install [TexMaker](https://www.xm1math.net/texmaker/)

# Getting biblatex to work

Install the [Better BibTex](https://github.com/retorquere/zotero-better-**bibtex**) extension for Zotero. To enable better exporting.

Install
```
sudo tlmgr install biber
```

In TexMaker preferences:
* In the Commands section, change Bib(la)tex to `biber %`.
* In the Quick Build section, change the command to "LaTeX + Bib(la)tex + LaTeX (x2) ... + View Pdf

In the document, if you don't have any citations, put `\nocite{*}` somewhere in the doc

# Useful stuff on getting a makefile going
* [Latexmk docs](https://mg.readthedocs.io/latexmk.html)
* [StackExchange answer on how to set up a makefile](https://tex.stackexchange.com/a/318595)
*