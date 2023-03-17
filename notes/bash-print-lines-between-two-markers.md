# Bash: Print lines that appear between two marker lines

e.g. If file.txt contained
```
BEGIN
a
b
c
END
```

You could print the lines between `BEGIN` and `END` with:

```
cat file.txt | sed -n '/^BEGIN/,/^END/p' | sed '1d;$d'
```

To include the markers in the output, drop the `| sed '1d;$d'` at the end.

There are some limitiations if the markers repeat etc. See [this StackExchange thread](https://unix.stackexchange.com/questions/264962/print-lines-of-a-file-between-two-matching-patterns) for more options. 
