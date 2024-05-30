#os #linux

## Copy & Cut & Move

```shell
cp [OPTION] SourcePath DestinationPath
```

# Options

| Option | Description                                                                 |
| ------ | --------------------------------------------------------------------------- |
| -a     | archive files                                                               |
| -f     | force copy by removing/overriding the destination file (if already present) |
| -i     | interactive mode override (asks before overwrite)                           |
| -l     | linkes the files instead copy                                               |
| -L     | follows symbolic links                                                      |
| -n     | no file overwrite, if destination file already exists                       |
| -R     | recursive copy (this includes hidden files)                                 |
| -u     | update mode i.e copy only when source is newer than dest                    |
| -v     | verbose mode i.e prints more information                                    || 


```shell
mv file "new file path"
```



## References

* https://www.guru99.com/linux-commands-cheat-sheet.html