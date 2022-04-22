# Bash Tricks

## Argument list too long when trying to copy files

```
find {source_dir} -name '{pattern}' -exec gcp -t {dest_dir} {} +
```
e.g `find ~/Downloads/images -name '*.jpeg' -exec gcp -t ./destination/images/ {} +`

This requires `gcp` because the built-in `cp` on MacOS does not support the -t (target directory) option. If it's not installed, run `brew install coreutils` to install it. 

In the find command, `-exec … {} +` means to execute the specified command for all the files. It invokes the command multiple times if necessary, taking care not to exceed the command line limit. The `+` also terminates the command (no need for `;`). [This stack overflow answer](https://unix.stackexchange.com/a/196917) gives a nice explanation.

## `xargs`: Control position of arguments in the command while still passing multiple arguments
Problem: You want to use `xargs` to send multiple arguments to a command, but you don't necessarily want the arguments to go at the end of the command e.g. when you want to pipe the output of a command to a file. `xargs` offers the `-I` option, allowing you to pass a replacement string, but this implies `-L 1`, meaning that xargs will pass arguments one at a time to the destination command. 

This illustrates the  problem and the solution:

Imagine we have a script `arg_count.sh` which just prints the number of arguments it's called with:
```bash
#!/bin/bash

echo Called with $# arguments
```

We can use this to test `xargs` as follows:
```bash
$ seq 1 10 | xargs arg_count.sh
Called with 10 arguments
```

If we supply `xargs` with enough arguments, it'll eventually break this into multiple invocations:
```bash
$ seq 1 10000 | xargs arg_count.sh
Called with 5000 arguments
Called with 5000 arguments
```

Now let's say we want to to send the output of all invocations to a log file. Now we want xargs to construct a command like this:

```
arg_count.sh ...arguments... >> logfile.txt
```

We can do this using the `-I` option:

```bash
seq 1 10000 | xargs -I {} arg_count.sh {} >> logfile.txt
```

But this results in `arg_count.sh` being invoked with just 1 argument at a time. After this, the contents of `logfile.txt` will be:

```
Called with 1 arguments
Called with 1 arguments
Called with 1 arguments
... and so on, 9997 more times ...
```

If we want it invoked with multiple arguments, we have to do this:

```bash
seq 1 10000 | xargs bash -c 'arg_count.sh "${@}" >> logfile.txt' bash
```

Now the contents of `logfile.txt` will be:

```
Called with 5000 arguments
Called with 5000 arguments
```

Here's what's going on:

The command passed to `xargs` is `bash -c 'arg_count.sh "${@}" >> logfile.txt' bash`. `xargs` will run this command, appending the arguments from it's input to the end. Effectively, it runs something like:

`bash -c 'arg_count.sh "${@}" >> logfile.txt' bash 1 2 3 ...`

Per the [man page for bash](https://linux.die.net/man/1/bash): 
> If the -c option is present, then commands are read from string. If there are arguments after the string, they are assigned to the positional parameters, starting with $0.

So everything after the string in single quotes gets assigned to positional arguments for the new bash shell. And what comes after that string is:

```
bash 1 2 3 ...
```

So `bash` is assigned to `$0` (this could really be any string but `bash` is normally what would be in `$0`), and the argument `1` goes in `$1`, `2` in `$2`, and so on. Effectively, the arguments passed by `xargs` become the positional arguments `$1` onward.

Inside the command string, we use `$@` to expand the positional parameters, starting from 1. So this then results in bash executing:

```
argcount.sh 1 2 3 ... >> logfile.txt
```

which is exactly what we want.







