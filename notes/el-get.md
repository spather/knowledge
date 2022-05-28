# el-get Package Manager for Emacs

Repo: https://github.com/dimitri/el-get

Info file: https://github.com/dimitri/el-get/blob/master/el-get.info

On integration with version control, the docs (see the section on Distributed Setup) are a little confusing but the key ideas are:
* The `.emacs.d/el-get/` directory contains the code for el-get itself and any installed packages. This whole folder should not be tracked, so add it to `.gitignore`
* The setup instructions have you put code in `init.el` that downloads/installs el-get if it's not already installed. On an established emacs setup, this should be a no-op.
* Below the initialization of el-get itself, `init.el` defines a list of packages that are required and then instructs el-get to download/install these if not present. The code that defines the list of packages is committed as part of `init.el`: this is how the list of required packages is maintained in version control. There is no equivalent of `package.json` or `package-lock.json`.
* To install new packages, add them to the list in init.el, so they will get properly tracked in version control. I've added code to `init.el` to remove any installed packages that are not in the defined list, to guard against packages being installed that are not tracked in version control. 

Some notes about the code in `init.el` that sets all this up:

* Starting from the end, the ultimate goal is to have in the variable `my-packages` a list of packages that we depend on. Then, these two lines cleanup (uninstall) any installed packages that are not in the list and the get (install if not already installed) all the packages in the list.

```elisp
(el-get-cleanup my-packages)
(el-get 'sync my-packages)
```

* The `my-packages` variable is built in a bit of a complicated way, because of how el-get makes you specify post-install initialization code. Here is the actual code:

```elisp
(setq el-get-sources
      '((:name buffer-move :after
               (progn
                 (global-set-key (kbd "<C-S-up>")    'buf-move-up)
                 (global-set-key (kbd "<C-S-down>")  'buf-move-down)
                 (global-set-key (kbd "<C-S-right>") 'buf-move-right)
                 (global-set-key (kbd "<C-S-left>")  'buf-move-left)))))

(setq my-packages
           (append
            '(el-get)
            (mapcar 'el-get-source-name el-get-sources)))
```
* The first part sets `el-get-sources`. El-get has a general mechanism where you can set `el-get-sources` to a list of local package recipes. We don't want to define a whole recipe but rather augment the regular definition of the package recipe (in this case for the package `buffer-move`) with some post-install initialization code (in this case, setting up some key bindings). When you include fields that can be in a recipe (e.g. the `:after` property) but don't specify `:type`, el-get looks for the recipe in the normal way in just merges in the fields we set here. So says [the docs](https://github.com/dimitri/el-get/blob/a620c91fe7d6d482c0e7538df75e10af0af1bb16/el-get.info#L859-L863). 

* The second part finally sets `my-packages`. The value is formed by appending to a hardcoded list of packages (here, just `el-get` itself), the names of the packages listed above in `el-get-sources`. Any packages that don't require custom post-install setup can be added to the hardcoded list part of this statement. In the end, `my-packages` becomes the full list of packages - those set via `el-get-sources` and those in the hardcoded list. 
