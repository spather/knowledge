# Bash Tricks

## Argument list too long when trying to copy files

```
find {source_dir} -name '{pattern}' -exec gcp -t {dest_dir} {} +
```
e.g `find ~/Downloads/images -name '*.jpeg' -exec gcp -t ./destination/images/ {} +`

This requires `gcp` because the built-in `cp` on MacOS does not support the -t (target directory) option. If it's not installed, run `brew install coreutils` to install it. 

In the find command, `-exec … {} +` means to execute the specified command for all the files. It invokes the command multiple times if necessary, taking care not to exceed the command line limit. The `+` also terminates the command (no need for `;`). [This stack overflow answer](https://unix.stackexchange.com/a/196917) gives a nice explanation.

