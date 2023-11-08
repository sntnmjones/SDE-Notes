## /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory

Find what provides the file
```
yum whatprovides /lib/ld-linux.so.2
```

See if you already have it
```
yum search glibc
```