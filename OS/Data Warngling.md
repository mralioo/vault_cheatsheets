#os #linux
[ref](https://missing.csail.mit.edu/2020/data-wrangling/)

## greb 

```shell
ssh myserver journalctl | grep sshd
```

```shell
$ ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' > ssh.log
$ less ssh.log
```
' ' : this command with run on the server not locally  



## sed 

stream editor that replaces text using matching expression with regex
```shell
sed 's/.*Disconnected from //' | less
```
s : subtitute expression
// : repalce with blank
/tz/ : repalce with tz e.g

## [regex](https://regex101.com/)

-   `.` means “any single character” except newline
-   `*` zero or more of the preceding match
-   `+` one or more of the preceding match
-   `[abc]` any one character of `a`, `b`, and `c`
-   `(RX1|RX2)` either something that matches `RX1` or `RX2`
-   `^` the start of the line
-   `$` the end of the line
-   `g`  do it multiple times 

```shell
echo 'abcaba' | sed -E 's/(ab|bc)*//g'
>> cc
```
-E : used with sed for new version regex

## awk
